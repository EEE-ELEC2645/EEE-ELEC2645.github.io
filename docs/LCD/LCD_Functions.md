---
title: LCD Functions Reference
parent: LCD
nav_order: 2
layout: default
---

# LCD Drawing Functions Reference Guide

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

This guide provides detailed explanations and examples for all the LCD drawing functions available in the ST7789V2 driver.

## Overview of Drawing Functions

All drawing functions modify the display buffer. None of them send data to the screen—you must call `LCD_Refresh()` afterwards to see the results.

```
Your Code              Buffer                 LCD Screen
┌─────────────────┐    ┌──────────┐          ┌────────┐
│ LCD_Draw_Rect() ├───>│ Modify   │          │        │
│ LCD_printString ├───>│ buffer   │          │ (no    │
│ etc.            ├───>│ in RAM   │    ──┐   │ change)│
└─────────────────┘    └──────────┘      │   └────────┘
                                         │
                            LCD_Refresh()│
                                         ▼
                                    ┌────────┐
                                    │Updated │
                                    └────────┘
```

## 1. LCD_Fill_Buffer() - Clear the Screen

### Purpose
Fill the entire display buffer with a single colour. Useful for clearing the screen or creating a background.

### Syntax
```c
void LCD_Fill_Buffer(uint8_t colour);
```

### Parameters
- `colour` - Colour index (0-15)

### Examples

**Clear to black:**
```c
LCD_Fill_Buffer(0);  // 0 = black (default palette)
LCD_Refresh(&cfg0);
```

**Create coloured background:**
```c
LCD_Fill_Buffer(1);  // 1 = white
LCD_printString("White Background", 10, 10, 0, 3);  // Black text on white
LCD_Refresh(&cfg0);
```

**Animation frame clear:**
```c
while(1) {
    LCD_Fill_Buffer(0);  // Clear previous frame
    // Draw new content here
    LCD_Refresh(&cfg0);
}
```

---

## 2. LCD_printString() - Draw Text

### Purpose
Display text on the screen. The text size can be scaled, but only one font is available (5×7 pixels per character).

### Syntax
```c
void LCD_printString(const char *str, uint16_t x, uint16_t y, 
                     uint8_t colour, uint8_t font_size);
```

### Parameters
- `str` - Text to display (null-terminated string)
- `x` - X coordinate (0-239) of top-left corner
- `y` - Y coordinate (0-239) of top-left corner
- `colour` - Text colour index (0-15)
- `font_size` - Scaling factor (1-8+)
  - 1 = 5×7 pixels per character
  - 2 = 10×14 pixels per character
  - 3 = 15×21 pixels per character
  - 5 = 25×35 pixels per character

### Examples

**Simple text:**
```c
LCD_Fill_Buffer(0);  // Black background
LCD_printString("Hello World", 10, 20, 1, 3);  // White text, scale 3
LCD_Refresh(&cfg0);
```

**Multiple text lines:**
```c
LCD_Fill_Buffer(0);
LCD_printString("Welcome to", 20, 10, 1, 5);   // Large, top
LCD_printString("ELEC2645", 20, 70, 2, 3);  // Red, middle
LCD_printString("Everyone!", 20, 140, 3, 2);    // Green, bottom
LCD_Refresh(&cfg0);
```

**Dynamic text with numbers (using sprintf):**
```c
int score = 12345;
char buffer[20];
snprintf(buffer, sizeof(buffer), "Score: %d", score);
LCD_printString(buffer, 10, 50, 1, 3);
LCD_Refresh(&cfg0);
```

### Font Size Considerations

```
Size 1: ABCDE     (5×7 pixels each)
Size 2: ABCDE     (10×14 pixels each)
Size 3: ABCDE     (15×21 pixels each)
Size 5: ABCDE     (25×35 pixels each)
```

**Tip:** Character width = font_size × 5, Height = font_size × 7

### Performance
Rendering text is moderately fast. More text = slower drawing. Always call `LCD_Refresh()` once after all text is drawn, not after each string.

```c
// Good - one refresh
LCD_Fill_Buffer(0);
LCD_printString("Line 1", 10, 10, 1, 3);
LCD_printString("Line 2", 10, 50, 1, 3);
LCD_printString("Line 3", 10, 90, 1, 3);
LCD_Refresh(&cfg0);

// Bad - three refreshes (slow)
LCD_printString("Line 1", 10, 10, 1, 3);
LCD_Refresh(&cfg0);
LCD_printString("Line 2", 10, 50, 1, 3);
LCD_Refresh(&cfg0);
LCD_printString("Line 3", 10, 90, 1, 3);
LCD_Refresh(&cfg0);
```

---

## 3. LCD_printChar() - Draw Single Character

### Purpose
Display a single character. Rarely used, since `LCD_printString()` is more flexible.

### Syntax
```c
void LCD_printChar(char ch, uint16_t x, uint16_t y, uint8_t colour);
```

### Parameters
- `ch` - Single character (use single quotes: `'A'` not `"A"`)
- `x` - X coordinate
- `y` - Y coordinate
- `colour` - Text colour (0-15)

**Note:** Unlike `LCD_printString()`, this function does NOT have a font_size parameter (always size 1).

### Examples

**Display single character:**
```c
LCD_Fill_Buffer(0);
LCD_printChar('A', 100, 100, 1);  // White 'A' at position 100,100
LCD_Refresh(&cfg0);
```

**Draw ASCII art:**
```c
LCD_Fill_Buffer(0);
for (int i = 0; i < 26; i++) {
    char c = 'A' + i;
    LCD_printChar(c, 10 + i*8, 10, 1);
}
LCD_Refresh(&cfg0);
```

---

## 4. LCD_Draw_Rect() - Draw Rectangles

### Purpose
Draw a rectangular shape, either filled or just the outline.

### Syntax
```c
void LCD_Draw_Rect(uint16_t x, uint16_t y, uint16_t width, uint16_t height,
                   uint8_t colour, uint8_t filled);
```

### Parameters
- `x`, `y` - Top-left corner coordinates
- `width` - Rectangle width in pixels
- `height` - Rectangle height in pixels
- `colour` - Rectangle colour (0-15)
- `filled` - 1 = filled, 0 = outline only

### Examples

**Simple filled rectangle:**
```c
LCD_Fill_Buffer(0);
LCD_Draw_Rect(50, 50, 100, 80, 2, 1);  // Red filled rectangle
LCD_Refresh(&cfg0);
```

**Rectangle outline:**
```c
LCD_Fill_Buffer(0);
LCD_Draw_Rect(50, 50, 100, 80, 5, 0);  // Green outline only
LCD_Refresh(&cfg0);
```

**Multiple rectangles (bar chart simulation):**
```c
LCD_Fill_Buffer(0);
int heights[] = {50, 80, 120, 90, 60};
for (int i = 0; i < 5; i++) {
    // Draw bar
    LCD_Draw_Rect(20 + i*40, 200 - heights[i], 35, heights[i], 2, 1);
}
LCD_Refresh(&cfg0);
```

**Button with border:**
```c
LCD_Fill_Buffer(0);
// Button background
LCD_Draw_Rect(75, 100, 100, 50, 3, 1);  // Green fill
// Button border
LCD_Draw_Rect(75, 100, 100, 50, 1, 0);  // White outline
// Button text
LCD_printString("PRESS ME", 85, 115, 0, 2);
LCD_Refresh(&cfg0);
```

### Coordinate System
```
(0,0) ----x---- (240,0)
 |
 y         (x,y) ---- width ----
 |         |                    |
 |         height               |
 |         |                    |
(0,240) -------- (240,240)
```

---

## 5. LCD_Draw_Circle() - Draw Circles

### Purpose
Draw a circular shape, either filled or just the outline.

### Syntax
```c
void LCD_Draw_Circle(uint16_t x_center, uint16_t y_center, uint16_t radius,
                     uint8_t colour, uint8_t filled);
```

### Parameters
- `x_center`, `y_center` - Center point coordinates
- `radius` - Circle radius in pixels
- `colour` - Circle colour (0-15)
- `filled` - 1 = filled, 0 = outline only

### Examples

**Filled circle:**
```c
LCD_Fill_Buffer(0);
LCD_Draw_Circle(120, 120, 50, 2, 1);  // Red filled circle
LCD_Refresh(&cfg0);
```

**Circle outline:**
```c
LCD_Fill_Buffer(0);
LCD_Draw_Circle(120, 120, 50, 1, 0);  // White outline
LCD_Refresh(&cfg0);
```

**Concentric circles:**
```c
LCD_Fill_Buffer(0);
for (int r = 10; r <= 50; r += 10) {
    LCD_Draw_Circle(120, 120, r, 1, 0);  // Rings
}
LCD_Refresh(&cfg0);
```

**Bouncing ball animation:**
```c
int x = 50, y = 50;
int dx = 3, dy = 2;

while(1) {
    LCD_Fill_Buffer(0);
    LCD_Draw_Circle(x, y, 8, 2, 1);  // Red filled ball
    LCD_Refresh(&cfg0);
    
    // Update position with bouncing
    x += dx;
    y += dy;
    if (x - 8 < 0 || x + 8 > 240) dx = -dx;
    if (y - 8 < 0 || y + 8 > 240) dy = -dy;
    
    HAL_Delay(20);
}
```

---

## 6. LCD_Draw_Line() - Draw Lines

### Purpose
Draw a straight line between two points.

### Syntax
```c
void LCD_Draw_Line(uint16_t x1, uint16_t y1, uint16_t x2, uint16_t y2,
                   uint8_t colour);
```

### Parameters
- `x1`, `y1` - Starting point
- `x2`, `y2` - Ending point
- `colour` - Line colour (0-15)

### Examples

**Horizontal line:**
```c
LCD_Fill_Buffer(0);
LCD_Draw_Line(10, 100, 230, 100, 1);  // Horizontal white line
LCD_Refresh(&cfg0);
```

**Diagonal lines (X pattern):**
```c
LCD_Fill_Buffer(0);
LCD_Draw_Line(0, 0, 240, 240, 1);     // \ diagonal
LCD_Draw_Line(240, 0, 0, 240, 1);     // / diagonal
LCD_Refresh(&cfg0);
```

**Grid pattern:**
```c
LCD_Fill_Buffer(0);
// Vertical lines
for (int x = 0; x <= 240; x += 30) {
    LCD_Draw_Line(x, 0, x, 240, 1);
}
// Horizontal lines
for (int y = 0; y <= 240; y += 30) {
    LCD_Draw_Line(0, y, 240, y, 1);
}
LCD_Refresh(&cfg0);
```

**Graph axes:**
```c
LCD_Fill_Buffer(0);
// Y-axis
LCD_Draw_Line(20, 10, 20, 220, 1);
// X-axis
LCD_Draw_Line(20, 220, 220, 220, 1);
LCD_printString("0", 15, 225, 1, 1);
LCD_Refresh(&cfg0);
```

---

## 7. LCD_Draw_Sprite() - Display Sprites

### Purpose
Display a pre-drawn image (sprite) from a 2D array. Pixels with value 255 are transparent.

### Syntax
```c
void LCD_Draw_Sprite(uint16_t x, uint16_t y, uint16_t width, uint16_t height,
                     const uint8_t *sprite);
```

### Parameters
- `x`, `y` - Top-left corner position
- `width`, `height` - Sprite dimensions in pixels
- `sprite` - Pointer to sprite data (cast 2D array to `uint8_t*`)

### Sprite Data Format

Sprites are 2D arrays where:
- Values 0-15 = colour indices
- Value 255 = transparent (not drawn)

```c
// 8×8 pixel smiley face
const uint8_t SMILEY[8][8] = {
  {255, 255, 0, 0, 0, 0, 255, 255},
  {255, 0, 0, 255, 255, 0, 0, 255},
  {0, 0, 255, 255, 255, 255, 0, 0},
  {0, 255, 255, 0, 0, 255, 255, 0},
  {0, 255, 255, 255, 255, 255, 255, 0},
  {0, 255, 0, 255, 255, 255, 255, 0},
  {255, 0, 0, 255, 255, 0, 0, 255},
  {255, 255, 0, 0, 0, 0, 255, 255}
};
```

### Examples

**Display sprite:**
```c
LCD_Fill_Buffer(0);
LCD_Draw_Sprite(100, 100, 8, 8, (uint8_t*)SMILEY);
LCD_Refresh(&cfg0);
```

**Multiple sprites:**
```c
LCD_Fill_Buffer(0);
LCD_Draw_Sprite(20, 20, 8, 8, (uint8_t*)SMILEY);
LCD_Draw_Sprite(50, 20, 8, 8, (uint8_t*)SMILEY);
LCD_Draw_Sprite(80, 20, 8, 8, (uint8_t*)SMILEY);
LCD_Refresh(&cfg0);
```

**Sprite in a loop:**
```c
for (int x = 0; x < 240; x += 20) {
    LCD_Fill_Buffer(0);
    LCD_Draw_Sprite(x, 100, 8, 8, (uint8_t*)SMILEY);
    LCD_Refresh(&cfg0);
    HAL_Delay(50);
}
```

---

## 8. LCD_Draw_Sprite_Colour() - Sprite with Colour Override

### Purpose
Draw a sprite but change all non-transparent pixels to a single colour.

### Syntax
```c
void LCD_Draw_Sprite_Colour(uint16_t x, uint16_t y, uint16_t width, uint16_t height,
                            const uint8_t *sprite, uint8_t colour);
```

### Parameters
Same as `LCD_Draw_Sprite()` plus:
- `colour` - Override colour for all non-transparent pixels

### Examples

**Same sprite, different colours:**
```c
LCD_Fill_Buffer(0);
LCD_Draw_Sprite_Colour(20, 50, 8, 8, (uint8_t*)SMILEY, 1);  // White
LCD_Draw_Sprite_Colour(50, 50, 8, 8, (uint8_t*)SMILEY, 2);  // Red
LCD_Draw_Sprite_Colour(80, 50, 8, 8, (uint8_t*)SMILEY, 3);  // Green
LCD_Refresh(&cfg0);
```

**Memory efficient:**
```c
// Instead of storing 15 different 8×8 smiley sprites...
// Store 1 sprite and reuse it with different colours!
for (int i = 0; i < 15; i++) {
    LCD_Draw_Sprite_Colour(10 + i*15, 50, 8, 8, (uint8_t*)SMILEY, i);
}
LCD_Refresh(&cfg0);
```

---

## 9. LCD_Draw_Sprite_Scaled() - Scaled Sprites

### Purpose
Draw a sprite at a larger size by scaling it up.

### Syntax
```c
void LCD_Draw_Sprite_Scaled(uint16_t x, uint16_t y, uint16_t width, uint16_t height,
                            const uint8_t *sprite, uint8_t scale);
```

### Parameters
Same as `LCD_Draw_Sprite()` plus:
- `scale` - Scaling factor (1=normal, 2=2×larger, 3=3×larger, etc.)

### Examples

**Scale comparison:**
```c
LCD_Fill_Buffer(0);
LCD_Draw_Sprite_Scaled(10, 50, 8, 8, (uint8_t*)SMILEY, 1);   // 8×8
LCD_Draw_Sprite_Scaled(40, 50, 8, 8, (uint8_t*)SMILEY, 2);   // 16×16
LCD_Draw_Sprite_Scaled(80, 50, 8, 8, (uint8_t*)SMILEY, 4);   // 32×32
LCD_Refresh(&cfg0);
```

**Zooming effect:**
```c
for (int scale = 1; scale <= 10; scale++) {
    LCD_Fill_Buffer(0);
    LCD_Draw_Sprite_Scaled(120 - 4*scale, 120 - 4*scale, 8, 8, 
                          (uint8_t*)SMILEY, scale);
    LCD_Refresh(&cfg0);
    HAL_Delay(100);
}
```

---

## 10. LCD_Draw_Sprite_Colour_Scaled() - Sprite with Both Colour and Scale

### Purpose
Combine colour override and scaling in one function.

### Syntax
```c
void LCD_Draw_Sprite_Colour_Scaled(uint16_t x, uint16_t y, uint16_t width, uint16_t height,
                                   const uint8_t *sprite, uint8_t colour, uint8_t scale);
```

### Examples

**Coloured and scaled sprites:**
```c
LCD_Fill_Buffer(0);
LCD_Draw_Sprite_Colour_Scaled(20, 50, 8, 8, (uint8_t*)SMILEY, 1, 2);   // White, 2×
LCD_Draw_Sprite_Colour_Scaled(60, 50, 8, 8, (uint8_t*)SMILEY, 2, 3);   // Red, 3×
LCD_Draw_Sprite_Colour_Scaled(120, 50, 8, 8, (uint8_t*)SMILEY, 3, 4);  // Green, 4×
LCD_Refresh(&cfg0);
```

---

## 11. LCD_Set_Palette() - Switch Colour Palettes

### Purpose
Change the colour palette used by the display (affects all colours immediately).

### Syntax
```c
void LCD_Set_Palette(LCD_Palette palette);
```

### Palette Options
- `PALETTE_DEFAULT` - Standard colours
- `PALETTE_GREYSCALE` - Black to white gradient
- `PALETTE_VINTAGE` - Warm, muted tones
- `PALETTE_CUSTOM` - User-defined (if configured)

### Examples

**Cycle through palettes:**
```c
LCD_Palette palettes[] = {PALETTE_DEFAULT, PALETTE_GREYSCALE, 
                          PALETTE_VINTAGE, PALETTE_CUSTOM};

for (int p = 0; p < 4; p++) {
    LCD_Set_Palette(palettes[p]);
    LCD_Refresh(&cfg0);
    HAL_Delay(2000);
}
```


## Performance Tips

1. **Batch drawing operations** - Draw everything to buffer before calling `LCD_Refresh()`
2. **Use sprites for repeated images** - More efficient than drawing shapes
3. **Colour override saves memory** - One sprite, many colours
4. **Scaling is computationally free** - Use scales liberally
5. **Minimise full screen clears** - Only clear areas that need it