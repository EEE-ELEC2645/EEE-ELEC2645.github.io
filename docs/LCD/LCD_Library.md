---
title: LCD Library Reference Guide
parent: LCD
nav_order: 1
layout: default
---

# LCD Display Programming Reference Guide

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


This guide explains the fundamental concepts behind displaying graphics on the ST7789V2 LCD screen using the STM32L476 microcontroller.

## Overview: The Display Loop

Working with an LCD display follows a simple three-step cycle that repeats:

```
┌─────────────────────────────────────────┐
│ 1. LCD_init() - Initialization          │
│    (run once at startup)                │
└────────────┬────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────┐
│ 2. Modify Buffer - Draw graphics        │
│    (LCD_printString, LCD_Draw_Rect, etc)│
└────────────┬────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────┐
│ 3. LCD_Refresh() - Send buffer to screen│
│    (repeat steps 2-3)                   │
└─────────────────────────────────────────┘
```

## 1. LCD_init() - Initialization

`LCD_init()` configures the LCD hardware for use. This is the most complex step, but you only need to do it once at the start of your program.

### Configuration Structure

Before calling `LCD_init()`, you must create a configuration structure that tells the driver:
- Which SPI peripheral to use (for serial communication)
- Which GPIO pins connect to which LCD signals

```c
ST7789V2_cfg_t cfg0 = {
  .setup_done = 0,              // Flag (0 = not setup yet)
  .spi = SPI2,                  // Use SPI2 peripheral
  
  // Pin configurations - maps LCD signals to Nucleo pins
  .RST = {.port = GPIOB, .pin = GPIO_PIN_2},      // Reset
  .BL = {.port = GPIOB, .pin = GPIO_PIN_1},       // Backlight
  .DC = {.port = GPIOB, .pin = GPIO_PIN_11},      // Data/Command
  .CS = {.port = GPIOB, .pin = GPIO_PIN_12},      // Chip Select
  .MOSI = {.port = GPIOB, .pin = GPIO_PIN_15},    // Data Out
  .SCLK = {.port = GPIOB, .pin = GPIO_PIN_13},    // Clock
  
  // DMA configuration for fast data transfers
  .dma = {.instance = DMA1, .channel = DMA1_Channel5}
};

LCD_init(&cfg0);  // Initialize with this configuration
```
If you are following the Lab instructions on Minerva, the above configuration is already provided in `main.c`, and will be the same for all projects using the LCD.

### What Happens Inside LCD_init()

When you call `LCD_init(&cfg0)`, the driver performs several setup steps:

1. **SPI Configuration** - Sets up the serial peripheral to communicate with the LCD at the correct speed - 40MHz
2. **GPIO Setup** - Configures the pins as outputs for control signals (CS, DC, RST) and input/output for data
3. **LCD Reset** - Depending on which breakout board is used, the display controller is reset using the RST pin or in software (as is the case with the Pimoroni board)
4. **Initialization Commands** - Sends a sequence of commands to the LCD controller to:
   - Set display orientation (240×240 pixels)
   - Enable the display
   - Configure colour mode (16-bit colour in RGB565 format)
5. **Backlight Enable** - Turns on the backlight so you can see the display
6. **Buffer Allocation** - Allocates memory for the display buffer (see next section)

### Why Configure Pins?

The LCD communicates with the Nucleo board through several GPIO pins and an SPI interface:

- **SPI Pins** (for data transfer):
  - MOSI (Master Out Slave In) - carries the image data
  - SCLK (Serial Clock) - synchronizes data transmission
  
- **Control Pins** (single bit signals):
  - CS (Chip Select) - tells the LCD you're sending data
  - DC (Data/Command) - tells the LCD if you're sending data (HIGH) or commands (LOW)
  - BL (Backlight) - enables/disables the LED backlight
  - (RST) (Reset) - hardware reset of the display (optional)

## 2. Display Buffer - Drawing Graphics

The LCD cannot draw directly. Instead, you:
1. Draw everything to a **buffer** in the microcontroller's memory
2. Send the entire buffer to the LCD screen when complete

The buffer is a 240×240 array of colour values:

```
┌─────────────────────────────────────────────┐
│ Your STM32L476 Memory                       │
│ ┌─────────────────────────────────────────┐ │
│ │ Display Buffer (240×240 pixels)         │ │
│ │ [Each pixel = 1 byte colour index]      │ │
│ │ pixel[0][0]  pixel[0][1] ... pixel[0]   │ │
│ │ pixel[1][0]  pixel[1][1] ... pixel[1]   │ │
│ │ ...                                     │ │
│ └─────────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
                    │
                    │ LCD_Refresh()
                    ▼
        ┌─────────────────────────────────┐
        │ ST7789V2 LCD Screen             │
        │ (displays the buffer contents)  │
        └─────────────────────────────────┘
```

### Benefits of Using a Buffer

**Performance:** Sending pixel data one at a time is slow. Buffering allows efficient block transfers via DMA (Direct Memory Access), much faster than CPU transfers.

**Consistency:** You can draw the complete next frame to the buffer while the current frame is displayed. This prevents flicker from partial updates.

**Memory Limitation:** The Nucleo STM32L476 has limited RAM. The display buffer uses significant memory, which is why we use 4-bit colour (indices 0-15) instead of full 16-bit RGB565 colour which would take 115,200 bytes - over 90% of the RAM available!

### Colour Indexing System

Instead of storing full colour values, we use **colour indices**:

```c
// The buffer stores indices 0-15
LCD_Fill_Buffer(2);  // Fill entire buffer with colour index 2

// The LCD driver maintains a colour palette
// palette[0] = black
// palette[1] = white
// palette[2] = red
// palette[3] = green
// ... etc
```

This saves memory: **4 bits per pixel** instead of **16 bits per pixel** (4× memory savings!)

### Buffer Operations

```c
// Fill entire buffer with one colour
LCD_Fill_Buffer(colour_index);

// Draw a circle at (x, y) with radius and colour
LCD_Draw_Circle(x, y, radius, colour_index, 1);  //1 = filled

// Clear a rectangular region
LCD_Draw_Rect(x, y, width, height, colour_index, 1);  // 1 = filled
```

## 3. LCD_Refresh() - Sending Buffer to Screen

### The Refresh Process

`LCD_Refresh(&cfg0)` transfers the entire buffer from the microcontroller's memory to the LCD display:

```
STM32L476 Memory          SPI Interface          LCD Screen
┌──────────────┐          ┌────────┐             ┌───────┐
│   Buffer     │─────────>│ SPI2   │────data────>│ VRAM  │
│ 240×240 px   │ (send)   │        │ (28.8 KB)   │       │
│ 28.8 KB      │          │ + DMA  │             │       │
└──────────────┘          └────────┘             └───────┘
                                                    │
                                                    ▼
                                              ┌───────────┐
                                              │ LCD Panel │
                                              │ (visible) │
                                              └───────────┘
```

### Transfer Method: DMA (Direct Memory Access)

The driver uses DMA to transfer data efficiently:
- **Without DMA**: CPU copies each byte -> slow, blocks everything
- **With DMA**: Hardware copies data while CPU does other things -> fast and responsive

### Step-by-Step Refresh

1. **Command Sequence** - Sends LCD controller commands to prepare for receiving pixel data
2. **Pixel Data Transfer** - Uses DMA to blast the 28.8 KB buffer to the LCD
3. **Update Complete** - The LCD displays the new image

### Typical Usage Pattern

```c
int main(void) {
    // ... setup code ...
    
    // Step 1: Initialize (once at startup)
    LCD_init(&cfg0);
    
    // Step 2-3: Main loop - repeat forever
    while(1) {
        
        // Draw frame to buffer
        LCD_Fill_Buffer(0);                    // Black background
        LCD_printString("Frame", 10, 10, 1, 5);
        
        // Send buffer to display
        LCD_Refresh(&cfg0);                    // Now visible on screen
        
        HAL_Delay(100);  // ~10 FPS
    }
}
```

### Performance Considerations

**Refresh Rate:** 
- Transferring 28.8 KB via SPI takes time, this will be the bottleneck in basically all of your projects.
- At typical speeds: ~16-30 FPS (frames per second)
- Add drawing time (LCD_printString, etc.) on top of refresh time

**Optimisation Techniques:**
```c
// Bad: Refreshing too often is wasteful
while(1) {
    LCD_Fill_Buffer(0);
    LCD_Refresh(&cfg0);     // 30 FPS, consumes power
    LCD_Refresh(&cfg0);     // Redundant!
}

// Good: Refresh only after changes
while(1) {
    LCD_Fill_Buffer(0);
    LCD_printString("Text", 10, 10, 1, 3);
    LCD_Refresh(&cfg0);     // Once per frame
    HAL_Delay(50);
}
```

## Complete Example: Animation Loop

Here's a complete example showing the full cycle with animation:

```c
int main(void) {
    HAL_Init();
    SystemClock_Config();
    MX_GPIO_Init();
    MX_USART2_UART_Init();
    
    // Step 1: Setup
    ST7789V2_cfg_t cfg0 = {
        .setup_done = 0,
        .spi = SPI2,
        .RST = {.port = GPIOB, .pin = GPIO_PIN_2},
        .BL = {.port = GPIOB, .pin = GPIO_PIN_1},
        .DC = {.port = GPIOB, .pin = GPIO_PIN_11},
        .CS = {.port = GPIOB, .pin = GPIO_PIN_12},
        .MOSI = {.port = GPIOB, .pin = GPIO_PIN_15},
        .SCLK = {.port = GPIOB, .pin = GPIO_PIN_13},
        .dma = {.instance = DMA1, .channel = DMA1_Channel5}
    };
    
    LCD_init(&cfg0);  // Initialize LCD
    
    // Step 2-3: Animation loop
    int x = 0;
    while(1) {
        // Clear frame
        LCD_Fill_Buffer(0);  // Black
        
        // Draw ball at position x
        LCD_Draw_Circle(x, 120, 10, 2, 1);  // Red filled circle
        
        // Send to display
        LCD_Refresh(&cfg0);
        
        // Move ball
        x += 5;
        if (x > 240) x = 0;
        
        HAL_Delay(50);  // ~20 FPS
    }
    
    return 0;
}
```

## Key Concepts Summary

| Concept | Purpose | Notes |
|---------|---------|-------|
| **LCD_init()** | One-time hardware setup | Configure pins, enable display, allocate buffer |
| **Buffer** | Drawing canvas in RAM | 240×240 pixels, 4-bit colour (indices 0-15) |
| **Drawing Functions** | Modify buffer pixels | LCD_printString, LCD_Draw_Circle, etc. |
| **LCD_Refresh()** | Send buffer to screen | Uses DMA for efficiency, takes ~30-50ms |
| **Colour Palette** | Map indices to colours | 16 different palettes available |

## Common Questions

**Q: Can I skip LCD_init()?**
A: No, the hardware won't work without it. The LCD controller and SPI interface must be properly configured.

**Q: What happens if I don't call LCD_Refresh()?**
A: Nothing changes on the screen. The screen continues displaying whatever was there before. The buffer in memory changes, but the LCD doesn't see it until you refresh.

**Q: Can I modify the buffer faster?**
A: Yes! For simple animations, only redraw the parts that change. But for simplicity, full refreshes work well for ~20 FPS.

**Q: Why 4-bit colour instead of full 16-bit?**
A: The STM32L476 only has 128 (96+32) KB of RAM. A full 16-bit buffer would need 115.2 KB, which nearly exceeds available memory. The 4-bit system uses 4× less memory (28.8 KB after compression).

---

## Advanced: Optimising Refresh with Dirty Rows

### How Partial Refresh Works

The LCD driver is smart about what it sends to the display. Instead of always sending the entire 28.8 KB buffer, it tracks which **rows** have changed and only sends those:

```
Frame 1:
LCD_Fill_Buffer(0);                    // Changes all 240 rows
LCD_Refresh(&cfg0);                    // Sends entire buffer (~50ms)

Frame 2:
LCD_printString("Score: 100", 10, 10, 1, 2);  // Changes maybe 20 rows
LCD_Refresh(&cfg0);                    // Sends only those 20 rows (~10ms)
```

When you modify pixels in the buffer, the driver marks those rows as changed. During `LCD_Refresh()`, it only transmits the changed rows to the LCD, then clears the flags. This is very similar to what is sometimes referred to as a "[dirty rectangle](https://wiki.c2.com/?DirtyRectangles)" optimisation.

This means you can optimise games and animations by only updating the parts of the screen that change!

### Example: Optimized Ball Animation

**Without optimisation (full buffer redraw each frame):**
```c
while(1) {
    LCD_Fill_Buffer(0);              // Clears entire buffer - marks all 240 rows dirty
    LCD_Draw_Circle(x, y, 10, 2, 1); // Draws ball
    LCD_Refresh(&cfg0);              // Sends all 240 rows (~50ms)
    
    x += 5;
    HAL_Delay(50);  // ~10 FPS due to refresh time
}
```

**With optimisation (only update changed area):**
```c
while(1) {
    // Only clear the small area where the ball was
    LCD_Draw_Rect(prev_x - 15, prev_y - 15, 30, 30, 0, 1);  // Clear old position
    
    // Draw ball at new position
    LCD_Draw_Circle(x, y, 10, 2, 1);
    
    LCD_Refresh(&cfg0);  // Sends only ~30 rows (~3ms)
    
    x += 5;
    prev_x = x;
    prev_y = y;
    
    HAL_Delay(50);  // Much more responsive - more drawing time!
}
```

### Key Optimisation Principle

**Instead of clearing the entire screen with `LCD_Fill_Buffer(0)`, clear only the regions that need updating.**

### Practical Example: Game with Static Background

```c
// Draw static background once
LCD_Fill_Buffer(0);  // Black background
LCD_Draw_Rect(20, 20, 200, 200, 1, 0);  // Game area border
LCD_printString("Score: 0", 100, 240, 1, 2);
LCD_Refresh(&cfg0);  // Full refresh (~50ms)

// Game loop - only update moving objects
int player_x = 100, player_y = 200;
int score = 0;

while(1) {
    // Clear only the player's old position
    LCD_Draw_Rect(player_x - 5, player_y - 5, 10, 10, 0, 1);
    
    // Move player
    if (button_pressed) {
        player_x += 10;
    }
    
    // Redraw player at new position
    LCD_Draw_Rect(player_x - 5, player_y - 5, 10, 10, 2, 1);
    
    // Update score (if changed)
    if (score_changed) {
        char buf[20];
        snprintf(buf, sizeof(buf), "Score: %d", score);
        LCD_printString(buf, 100, 240, 1, 2);
    }
    
    LCD_Refresh(&cfg0);  // Only changed rows sent! (~5ms)
    
    HAL_Delay(30);  // ~30 FPS instead of 10 FPS!
}
```

### General Strategy for Optimisation

1. **Draw static elements once** - Background, borders, HUD, text that doesn't change
2. **Only clear small areas** - Don't use `LCD_Fill_Buffer()` every frame
3. **Update only what moves** - Players, projectiles, animated objects
4. **Keep track of old positions** - So you know what to clear
5. **Refresh after each update** - The driver handles sending only changed rows

### Important Notes

- The optimisation is **automatic** - you don't need to do anything special
- The driver handles dirty row tracking internally
- You just need to be smart about what you modify in the buffer
- It's especially useful for games with moving sprites and static backgrounds
