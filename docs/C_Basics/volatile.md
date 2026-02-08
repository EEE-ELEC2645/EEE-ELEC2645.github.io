---
title: Volatile
parent: C Basics
nav_order: 13
layout: default
---

# The `volatile` Keyword

`volatile` tells the compiler that a variable can change **outside** the normal flow of your code. This prevents the compiler from optimising away reads or writes that must always happen.

## Why It Matters in Embedded Systems

In embedded projects, variables often change due to **hardware or interrupts**, not just your main code. Common examples include:

- **Interrupt Service Routines (ISRs)** setting a flag
- **Hardware registers** that update as peripherals run
- **DMA** writing into a buffer while your code is reading it

If the compiler thinks a variable never changes, it might cache it in a register and never read the real memory again. That breaks embedded code.

## Simple Example: ISR Flag

```c
volatile int g_timer_flag = 0;

void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
    g_timer_flag = 1;  // Set by interrupt
}

int main(void)
{
    while (1) {
        if (g_timer_flag) {
            g_timer_flag = 0;  // Clear in main loop
            // Do something
        }
    }
}
```

Here, `g_timer_flag` is changed by the ISR, so it **must** be `volatile`. Otherwise, the compiler may optimise the `if (g_timer_flag)` check in a way that never sees the ISR update.

Use it whenever a variable can change unexpectedly from your code's point of view.
