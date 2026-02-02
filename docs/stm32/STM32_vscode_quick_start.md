---
title: VScode Quick Start
parent: STM32
nav_order: 0
layout: default
---

# STM32 Project Quick Start

**TL;DR:** Edit `main.c` and run. That's basically it.

---

## Your Project Folder

```
Unit_3_1_LCD_Test/
├── Core/Src/main.c          ← <<< EDIT THIS
├── Core/Inc/main.h
├── Drivers/                  ← Don't touch
├── ST7789V2_Driver_STM32L4/  ← Don't touch (LCD functions)
└── build/                    ← Don't touch (auto-generated)
```

---

## What To Do

### 1. Open `Core/Src/main.c`
This is your program. It looks like:

```c
int main(void) {
    HAL_Init();
    SystemClock_Config();
    MX_GPIO_Init();
    
    // ← Write your code here!
    
    while(1) {
        // ← Or here for loops
    }
}
```

### 2. Use These Functions

**Display:**
```c
#include "LCD.h"

LCD_init();                          // Start the display
LCD_Draw_Circle(x, y, radius, color); // Draw circle
LCD_printString(x, y, "Hello", color); // Draw text
LCD_Refresh();                       // Show changes
```

**Hardware:**
```c
HAL_GPIO_WritePin(port, pin, state);  // Digital output
HAL_GPIO_ReadPin(port, pin);          // Digital input
HAL_Delay(milliseconds);              // Wait
printf("Debug: %d\n", value);         // Serial output
```

### 3. Build & Run
- Click the Build button (hammer icon)
- Click Run button
- Done!

---

## Files You Might Edit

| File | When? |
|------|-------|
| `main.c` | Always - your code goes here |
| `main.h` | Sometimes - for global variables/functions |
| `.ioc` file (in CubeMX) | Only if changing hardware pins |

---

## Files to Leave Alone

- `Drivers/` - External libraries
- `ST7789V2_Driver_STM32L4/` - LCD driver
- `build/` - Auto-generated output
- Everything with "auto-generated" in the comment

---

## Common Questions

**Q: Do I need to understand the whole project structure?**
No! Just focus on `main.c`. Everything else "just works."

**Q: Where do the LCD functions come from?**
From the `ST7789V2_Driver_STM32L4/` folder. Just `#include "LCD.h"` and use them.

**Q: My code isn't working. Where do I debug?**
1. Check `main.c` for typos
2. Use `printf()` to print debug info
3. See [Full Guide](STM32_vscode.md) for more details

**Q: What happens when I press Build?**
1. **Compile** - Your `.c` files get turned into machine code the board can run
2. **Link** - All the pieces (your code + libraries) get connected together
3. **Output** - A `.elf` file is created in the `build/` folder (this is your program)
4. **Upload** - VS Code flashes it onto the microcontroller

If there are errors, VS Code shows them. Fix the error and press Build again. It's fast - usually takes 2-5 seconds!

---

## Next Steps

- Check the [LCD Library Reference](../LCD/LCD_Library.md) for available functions
- Look at example projects in the [Unit_3_1_LCD_Test repo](https://github.com/EEE-ELEC2645/Unit_3_1_LCD_Test)
- Read the [Full Project Guide](STM32_vscode.md) if you want to understand the deep structure

---

## Need More Details?

The [Full Project Structure Guide](STM32_vscode.md) explains everything in detail - but you don't need it to get started!
