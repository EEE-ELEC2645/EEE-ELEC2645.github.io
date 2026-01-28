---
title: Colour Palette System
parent: LCD
nav_order: 3
layout: default
---


# LCD Colour Palette System Reference Guide

This guide explains how the colour palette system works and how to use different colour schemes in your LCD applications.

## Why Colour Palettes?

Instead of storing full 16-bit RGB colour values (which would use too much memory), the LCD driver uses a **colour palette system**:

```
Your Code                 Display Buffer             LCD Screen
┌──────────────────┐      ┌──────────────┐         ┌──────────┐
│ Colour Index 0   │ ───> │ [0][0][0]... │ ──────> │ Black    │
│ Colour Index 2   │      │ [2][2][2]... │         │ Red      │
│ (etc.)           │      │              │         │ (physical)
└──────────────────┘      └──────────────┘         └──────────┘
                                │
                    LCD_Set_Palette()
                                │
                          ┌─────▼──────┐
                          │  Palette   │
                          │ [0] = 0x000│ (Black)
                          │ [1] = 0xFFF│ (White)
                          │ [2] = 0xF00│ (Red)
                          │ ...        │
                          └────────────┘
```

**Key Principle:** The buffer stores **indices (0-15)**, not colours. The palette maps those indices to actual colours.

---

## The Colour Index System

The display buffer stores values 0-15, each representing a colour from the palette.

```c
// In your code:
LCD_Fill_Buffer(2);  // Fill buffer with value 2

// What the palette does:
// If PALETTE_DEFAULT:  2 = Red
// If PALETTE_GREYSCALE: 2 = Dark grey
// If PALETTE_VINTAGE:   2 = Brownish tone

// The LCD displays the colour mapped by the current palette
```

### Index Reference

| Index | Usage |
|-------|-------|
| 0 | Usually background/darkest |
| 1 | Usually brightest |
| 2-15 | Various colours |
| 255 (in sprites) | Transparent - don't draw |

---

## Available Palettes

### 1. PALETTE_DEFAULT - Standard Colours

The default palette provides standard, vibrant colours:

```
Index   Colour Name        Hex Code    Visual
  0     Black              0x000       
  1     White              0xFFF       
  2     Red                0xF00       
  3     Green              0x0F0       
  4     Blue               0x00F       
  5     Yellow             0xFF0       
  6     Cyan               0x0FF       
  7     Magenta            0xF0F       
  8     Dark Red           0x800
  9     Dark Green         0x080
 10     Dark Blue          0x008
 11     Orange             0xF80
 12     Purple             0x808
 13     Teal               0x088
 14     Brown              0x840
 15     Grey               0x888
```

**Best for:** Games, educational apps, vibrant visual demonstrations

### 2. PALETTE_GREYSCALE - Black to White Gradient

A grayscale palette ranging from black to white:

```
Index   Brightness Level   Hex Code    Visual
  0     Black              0x000       
  1     Very Dark          0x111
  2     Dark               0x222
  3     Dark-Medium        0x333
  4     Medium-Dark        0x444
  5     Medium             0x555       
  6     Medium-Light       0x666
  7     Light-Medium       0x777
  8     Light              0x888
  9     Light-Bright       0x999
 10     Bright-Light       0xAAA
 11     Bright             0xBBB       
 12     Very Bright        0xCCC
 13     Brighter           0xDDD
 14     Very Bright        0xEEE
 15     White              0xFFF       
```

**Best for:** Data visualization, charts, medical/industrial displays

**Smooth gradient example:**
```c
LCD_Set_Palette(PALETTE_GREYSCALE);

// Draw a gradient from dark to light
for (int i = 0; i <= 15; i++) {
    LCD_Draw_Rect(10 + i*15, 50, 14, 100, i, 1);  // Gradual brightening
}
LCD_Refresh(&cfg0);
```

### 3. PALETTE_VINTAGE - Warm, Muted Tones

A palette with warm, retro colours inspired by vintage computing:

```
Index   Colour Name        Hex Code    Appearance
  0     Warm Black         0x111       Deep, rich black
  1     Cream              0xFEA       Warm off-white
  2     Rust Red           0xB33       Muted red
  3     Sage Green         0x8B5       Muted green
  4     Slate Blue         0x558       Muted blue
  5     Mustard Yellow     0xCC8       Warm yellow
  6     Teal               0x8BB       Teal tone
  7     Mauve              0xB8B       Purplish grey
  8     Dark Rust          0x844       Dark red-brown
  9     Forest Green       0x464       Dark green
 10     Navy Blue          0x224       Dark blue
 11     Gold               0xB84       Burnished gold
 12     Purple             0x665       Muted purple
 13     Cyan-Green         0x589       Teal-cyan
 14     Terracotta         0x953       Earthy orange
 15     Ash Grey           0x999       Neutral grey
```

**Best for:** Retro games, comfortable user interfaces, warm aesthetics

**Vintage game example:**
```c
LCD_Set_Palette(PALETTE_VINTAGE);

LCD_Fill_Buffer(0);  // Warm black background

// Old-school game title
LCD_printString("LUNAR LANDER", 20, 20, 1, 4);  // Cream text
LCD_Draw_Rect(30, 80, 180, 100, 8, 0);          // Rust red border
LCD_Refresh(&cfg0);
```

### 4. PALETTE_CUSTOM - User-Defined Colours

You can create your own custom palette by modifying the palette definition in the LCD driver (usually in `LCD.h`).

```c
// Custom palette for a space theme
// (This would go in LCD.h or your configuration)
const uint16_t PALETTE_CUSTOM_DATA[16] = {
    0x000,  // 0: Black space
    0x0FF,  // 1: Cyan stars
    0xF0F,  // 2: Magenta nebula
    0xFF0,  // 3: Yellow Sun
    0xFFF,  // 4: White bright stars
    0x088,  // 5: Dark teal
    0x800,  // 6: Dark red
    // ... etc for indices 7-15
};
```

Then use it:
```c
LCD_Set_Palette(PALETTE_CUSTOM);
LCD_Refresh(&cfg0);
```

---

## Switching Palettes at Runtime

You can change palettes during program execution. All colours immediately shift to the new palette:

```c
// Start with default
LCD_Set_Palette(PALETTE_DEFAULT);
LCD_Fill_Buffer(2);  // Red fill
LCD_Refresh(&cfg0);
HAL_Delay(2000);

// Switch to greyscale - same content, different colours!
LCD_Set_Palette(PALETTE_GREYSCALE);
LCD_Refresh(&cfg0);  // Now it's dark grey (medium index)
HAL_Delay(2000);
```

---

## Using Colour Indices Effectively

### Semantic Colour Use

Instead of hardcoding numbers, use colour indices meaningfully:

```c
// Less clear:
LCD_printString("Error", 10, 10, 2, 3);  // What colour is 2?

// Better - define constants:
#define COLOUR_TEXT_NORMAL    1   // White
#define COLOUR_TEXT_ERROR     2   // Red (in default palette)
#define COLOUR_TEXT_SUCCESS   3   // Green
#define COLOUR_BACKGROUND     0   // Black

LCD_Fill_Buffer(COLOUR_BACKGROUND);
LCD_printString("Error", 10, 10, COLOUR_TEXT_ERROR, 3);
LCD_Refresh(&cfg0);
```

### Palette-Aware Design

Design your code to work with any palette:

```c
void draw_game_screen(void) {
    LCD_Fill_Buffer(0);              // Always index 0 (background)
    
    LCD_printString("Score", 10, 10, 1, 3);     // Index 1 (foreground)
    
    // Draw player
    LCD_Draw_Sprite_Colour(100, 100, 8, 8, PLAYER_SPRITE, 2);  // Index 2 (primary)
    
    // Draw enemies
    LCD_Draw_Sprite_Colour(50, 50, 8, 8, ENEMY_SPRITE, 4);     // Index 4 (secondary)
    
    LCD_Refresh(&cfg0);
}

// Now this works with any palette!
LCD_Set_Palette(PALETTE_DEFAULT);
draw_game_screen();  // Displays in default colours
HAL_Delay(3000);

LCD_Set_Palette(PALETTE_VINTAGE);
draw_game_screen();  // Same screen, warm tones
HAL_Delay(3000);
```

---

## Performance and Memory Notes

Switching palettes is **very fast** - it just remaps the colour values, doesn't redraw pixels.

```c
// Fast: just change palette
LCD_Set_Palette(PALETTE_GREYSCALE);      // Instant (µs)
LCD_Refresh(&cfg0);                      // Still ~30-50ms for transfer

// Slow: redraw everything
LCD_Fill_Buffer(0);
// Draw lots of stuff
LCD_Refresh(&cfg0);                      // Much slower
```

This makes palettes excellent for:
- Theme switching
- Time-based effects
- Mode changes (pause menu, level complete, etc.)
- Accessibility features

---

## RGB to 4-bit Index Conversion

If you want to add a custom palette, 

## Creating Custom Palette Colours

### Step 1: Find Your RGB565 Colour

Use an online RGB565 converter you can find colours [here](https://rgbcolorpicker.com/565) or whole palettes [here] (https://lospec.com/palette-list) to get the hex value. For example:
- Bright green: `0x07E0`
- Sky blue: `0x87FF`
- Orange: `0xFD20`

### Step 2: Add to LCD.h with Bytes Swapped

Open [ST7789V2_Driver_STM32L4/Core/Inc/LCD.h](ST7789V2_Driver_STM32L4/Core/Inc/LCD.h) and add your colour as a define with the bytes swapped:

```c
// Add your custom colours at the top of LCD.h, near other colour definitions
#define MY_BRIGHT_GREEN  0xE007  // Original: 0x07E0, bytes swapped
#define MY_SKY_BLUE      0xFF87  // Original: 0x87FF, bytes swapped
#define MY_ORANGE        0x20FD  // Original: 0xFD20, bytes swapped
```

### Step 3: Use in Your Palette

Add these colour definitions to your custom palette array:

```c
// In LCD.h, in the palette array section:
const uint16_t PALETTE_CUSTOM_DATA[16] = {
    0x000,           // 0: Black (background)
    0xFFF,           // 1: White (text)
    MY_BRIGHT_GREEN, // 2: Your custom green
    MY_SKY_BLUE,     // 3: Your custom blue
    MY_ORANGE,       // 4: Your custom orange
    // ... rest of palette
};
```

### Step 4: Use the Index in Your Code

Now you can use the colour index in your drawing functions:

```c
LCD_Fill_Buffer(0);                          // Black background
LCD_printString("Nature", 10, 10, 2, 3);    // Green text (index 2)
LCD_Draw_Circle(100, 100, 30, 3, 1);        // Blue circle (index 3)
LCD_Draw_Rect(50, 150, 80, 60, 4, 1);       // Orange rectangle (index 4)
LCD_Refresh(&cfg0);
```

### Quick Reference: Byte Swapping

To swap bytes in RGB565:
```
Original:  0xABCD
Swapped:   0xCDAB

In code:   uint16_t swapped = ((original & 0xFF) << 8) | ((original >> 8) & 0xFF);
```