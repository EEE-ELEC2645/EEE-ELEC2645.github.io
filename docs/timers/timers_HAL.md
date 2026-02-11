---
title: Timers in HAL
parent: Timers
nav_order: 2
layout: default
math: katex
---

# Practical Timer Implementation with STM32 HAL

Now that we understand the theory behind timers, let's implement a practical example using STM32 HAL. This section demonstrates how to configure and use a hardware timer to trigger interrupts without blocking your program.

## The Problem We're Solving

Going back to the LED blinking example from the introduction: we want an LED to toggle every 500ms whilst remaining responsive to other operations. Using `HAL_Delay()` blocks the entire program, and polling with `HAL_GetTick()` wastes CPU cycles. The hardware timer solution is perfectly suited to this task.

## Hardware Timer Setup with STM32CubeMX

Before writing code, you need to configure the timer in STM32CubeMX, this has already been one for you in the provided project template (see `tim.c` and `tim.h` for the generated code). The steps to set it up are:

1. **Enable the Timer**: Open your `.ioc` file and select a basic timer (like TIM6 for simple timing tasks).
2. **Configure the Prescaler**: Set it to produce a convenient tick frequency. For an 80 MHz system clock, a prescaler of 7999 produces a 10 kHz tick frequency.
3. **Enable Interrupts**: Use the NVIC settings to enable the timer's update interrupt.
4. **Generate Code**: STM32CubeMX will automatically generate `MX_TIM6_Init()` and the timer handle `htim6`.

## Interrupt-Driven Timer Example

The most efficient approach uses **timer interrupts**. When the timer period elapses, it triggers an interrupt, allowing your main program to continue executing uninterrupted until the ISR fires.

### Key Components

**1. Volatile Flag for Inter-ISR Communication**

```c
// Must be volatile — the ISR changes it, main loop reads it
volatile int g_timer_flag = 0;
int led_state = 0;
```

The `volatile` keyword tells the compiler not to optimise away reads of this variable, as they change unexpectedly (from the ISR's perspective).

**2. Timer Initialisation**

```c
// In main():
HAL_TIM_Base_Init(&htim6);
HAL_TIM_Base_Start_IT(&htim6);  // Start with interrupts enabled
```

`HAL_TIM_Base_Start_IT()` starts the timer and enables its update interrupt. The timer now counts automatically in the background.

**3. Main Loop — Non-Blocking**

```c
while (1)
{
    // Check if timer interrupt has occurred
    if (g_timer_flag) {
        g_timer_flag = 0;  // Clear the flag
        
        // Toggle LED state
        led_state = !led_state;
        HAL_GPIO_WritePin(GPIOA, GPIO_PIN_9, 
                         led_state ? GPIO_PIN_SET : GPIO_PIN_RESET);
    }
    
    // Put the MCU to sleep until an interrupt wakes it up
    __WFI();  // Wait for interrupt
}
```

Notice:
- The main loop checks the flag set by the ISR, rather than blocking.
- `__WFI()` (Wait For Interrupt) puts the CPU into a low-power sleep state until an interrupt wakes it.
- Other code can run between timer interrupts.

**4. Interrupt Service Routine (ISR) Callback**

```c
void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
    if (htim->Instance == TIM6) {
        g_timer_flag = 1;  // Set flag to indicate timer interrupt occurred
    }
}
```

This callback is called automatically by HAL when TIM6's period elapses. By checking which timer fired, you can use one callback for multiple timers if needed.

## Calculating the Timer Period

The helper function below calculates the ARR (Auto-Reload Register) value for a desired interval:

```c
/**
  * @brief Calculate TIM6 period value from desired interrupt interval
  * @param interval_ms: Desired interrupt interval in milliseconds
  * @retval uint32_t: Period value for TIM6
  * 
  * TIM6 is configured with:
  *   - Prescaler: 7999 (divides 80MHz clock down to 10kHz)
  *   - Tick interval: 0.1ms
  *   - Period = (interval_ms × 10) - 1
  */
uint32_t get_timer_period(uint32_t interval_ms)
{
    return (interval_ms * 10) - 1;
}
```

**How it works:**
- With prescaler 7999: $f_{tick} = \frac{80,000,000}{8,000} = 10,000$ Hz
- Tick period: $\frac{1}{10,000} = 0.1$ ms per tick
- For a 100ms interval: ARR $= (100 \times 10) - 1 = 999$
- Output frequency: $f = \frac{10,000}{1,000} = 10$ Hz

**Example usage:**

```c
// Configure for 100ms timer interrupt
htim6.Init.Period = get_timer_period(100);
HAL_TIM_Base_Init(&htim6);
HAL_TIM_Base_Start_IT(&htim6);
```

**Changing the timer update rate at runtime:**

When you change `htim6.Init.Period` after the timer is running, you must stop the timer, re-initialise it, and then start it again. Otherwise the new period will not take effect.

```c
// Change timer interval safely while running
HAL_TIM_Base_Stop_IT(&htim6);
htim6.Init.Period = get_timer_period(100);  // or get_timer_period(500)
HAL_TIM_Base_Init(&htim6);
HAL_TIM_Base_Start_IT(&htim6);
```

## Complete Working Example

Here's a complete example that toggles an LED every 100ms using TIM6:

```c
#include "main.h"
#include "gpio.h"
#include "tim.h"

volatile int g_timer_flag = 0;
int led_state = 0;

uint32_t get_timer_period(uint32_t interval_ms)
{
    return (interval_ms * 10) - 1;
}

int main(void)
{
    HAL_Init();
    SystemClock_Config();
    MX_GPIO_Init();
    MX_TIM6_Init();
    
    // Ensure LED starts OFF
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_9, GPIO_PIN_RESET);
    
    // Configure and start timer (100ms interval)
    htim6.Init.Period = get_timer_period(100);
    HAL_TIM_Base_Init(&htim6);
    HAL_TIM_Base_Start_IT(&htim6);
    
    while (1)
    {
        if (g_timer_flag) {
            g_timer_flag = 0;
            
            // Toggle LED
            led_state = !led_state;
            HAL_GPIO_WritePin(GPIOA, GPIO_PIN_9, 
                             led_state ? GPIO_PIN_SET : GPIO_PIN_RESET);
        }
        
        __WFI();  // Sleep until next interrupt
    }
}

void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
    if (htim->Instance == TIM6) {
        g_timer_flag = 1;
    }
}
```

## Key Advantages of This Approach

| Feature | HAL_Delay | Polling HAL_GetTick | Timer Interrupt |
|---------|-----------|-------------------|-----------------|
| **Blocks CPU** | Yes ❌ | No ✓ | No ✓ |
| **Wastes cycles** | Yes ❌ | Yes ❌ | No ✓ |
| **Power efficient** | No ❌ | No ❌ | Yes ✓ |
| **Can respond to events** | No ❌ | Yes ✓ | Yes ✓ |
| **Complexity** | Low | Medium | Medium |

Timer interrupts provide the best balance: the CPU sleeps until the timer fires, consuming minimal power whilst remaining responsive.

## Tips and Best Practices

1. **Use `volatile` for flags**: Always mark variables shared between the main loop and ISR as `volatile`.
2. **Keep ISRs short**: Don't perform heavy computation in the callback; just set a flag and let the main loop do the work.
3. **Choose appropriate timers**: 
   - **TIM6/TIM7**: Simple timing (no GPIO output)
   - **TIM2/TIM3/TIM4/TIM5**: General-purpose timers with PWM capability
4. **Prescaler selection**: Choose a prescaler that gives you good resolution. Example: for millisecond timing, 10 kHz is convenient.
5. **Account for jitter**: Timer interrupts are accurate, but the main loop's response time varies. For critical timing, use the timer's compare registers directly.

## Next Steps

Now that you understand basic timer interrupts, you can explore:
- Using multiple timers simultaneously
- PWM (Pulse Width Modulation) with timers