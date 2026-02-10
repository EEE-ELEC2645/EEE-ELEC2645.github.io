---
title: PWM in HAL
parent: PWM
nav_order: 2
layout: default
math: katex
---

# Practical PWM Implementation with STM32 HAL

This section mirrors the timers HAL workflow, but focuses on configuring and using PWM with STM32 HAL. The goal is to generate a stable PWM signal for lab tasks like LED dimming and motor control, while keeping your CPU free for other work.

## The Problem We're Solving

We want a PWM signal with a **fixed frequency** and a **variable duty cycle**. Manually toggling a GPIO pin wastes CPU time and produces jitter. The correct solution is to use a timer in PWM mode so the hardware generates the waveform automatically.

## Hardware PWM Setup with STM32CubeMX

Before writing code, configure PWM in STM32CubeMX. In the provided lab projects, this is already set up for you (see `tim.c` and `tim.h` for generated code), including PB6 mapped to TIM4_CH1 (AF2). The typical steps are:

1. **Select a timer with PWM capability** (TIM4 is used in this lab).
2. **Enable a PWM channel** (TIM4 Channel 1) and map it to **PB6**.
3. **Choose a prescaler and period** to get your target PWM frequency.
4. **Generate code** to create `MX_TIM4_Init()` and the timer handle `htim4`.

Again, we have already done this for you in the lab template, so you can jump straight to the code section below. :D

## Key PWM Parameters

The timer controls PWM using two values:

- **ARR (Period)**: total timer counts for one PWM cycle
- **CCR (Compare/Pulse)**: the count at which the output changes state

The duty cycle is:

$$
	\text{Duty Cycle (\%)} = \frac{\text{CCR}}{\text{ARR}} \times 100
$$

The PWM frequency is:

$$
f_{pwm} = \frac{f_{timer}}{ARR + 1}
$$

where:

$$
f_{timer} = \frac{f_{clock}}{\text{Prescaler} + 1}
$$

## HAL PWM Startup

Once CubeMX has generated the timer setup, starting PWM is simple:

```c
// In main():
MX_TIM4_Init();
HAL_TIM_PWM_Start(&htim4, TIM_CHANNEL_1);
```

At this point the PWM signal appears on the configured GPIO pin, with the period and pulse values you set in CubeMX.

## Changing Duty Cycle in Code

The duty cycle is controlled by the compare (pulse) register. Use the HAL macro `__HAL_TIM_SET_COMPARE()` to update it at runtime:

```c
// Example: set duty cycle to 25% for ARR=999
uint32_t arr = __HAL_TIM_GET_AUTORELOAD(&htim4); // current period
uint32_t pulse = (arr + 1) / 4;                  // 25%
__HAL_TIM_SET_COMPARE(&htim4, TIM_CHANNEL_1, pulse);
```

This updates the duty cycle without stopping the timer, which is ideal for smooth LED fading or motor speed control.

## Example: LED Brightness Control

This example assumes TIM4 CH1 is mapped to PB6 driving an LED:

```c
#include "main.h"
#include "tim.h"

int main(void)
{
	HAL_Init();
	SystemClock_Config();
	MX_GPIO_Init();
	MX_TIM4_Init();

	// Start PWM output
	HAL_TIM_PWM_Start(&htim4, TIM_CHANNEL_1);

	while (1)
	{
		// Ramp duty cycle up and down
		for (uint32_t duty = 0; duty <= 100; duty++) {
			uint32_t arr = __HAL_TIM_GET_AUTORELOAD(&htim4);
			uint32_t pulse = (arr + 1) * duty / 100;
			__HAL_TIM_SET_COMPARE(&htim4, TIM_CHANNEL_1, pulse);
			HAL_Delay(10);
		}
		for (int duty = 100; duty >= 0; duty--) {
			uint32_t arr = __HAL_TIM_GET_AUTORELOAD(&htim4);
			uint32_t pulse = (arr + 1) * duty / 100;
			__HAL_TIM_SET_COMPARE(&htim4, TIM_CHANNEL_1, pulse);
			HAL_Delay(10);
		}
	}
}
```

This uses PWM to dim the LED smoothly by varying the duty cycle from 0% to 100%.

## Calculating a Common PWM Frequency

If your system clock is 80 MHz and you want a 1 kHz PWM signal:

1. Choose a prescaler to get a convenient timer tick.
   - Prescaler = 79 gives $f_{timer} = 1$ MHz (1 us per tick)
2. Choose ARR for the PWM period.
   - 1 kHz period is 1000 us, so ARR = 999

With these values, the PWM period is 1 ms and you can set CCR to any value from 0 to 999 to control duty cycle.

## Tips and Best Practices

1. **Pick a stable frequency first**: Many devices expect a fixed PWM frequency (e.g. motors and LEDs).
2. **Update duty cycle only**: Keep the period constant and adjust CCR for control.
3. **Use the right timer**: Advanced timers (TIM1/TIM8) support dead-time and complementary outputs for motor drivers.
4. **Avoid CPU-heavy loops**: PWM generation is hardware-based, so your main loop should focus on logic, not timing.

## Next Steps

Now that you can generate PWM with HAL, you can move on to:

- Looking at the PWM library which abstracts these details for you
- Using the Buzzer with PWM for sound generation
- Try looking at the PWM output with an oscilloscope or logic analyzer to see the waveform in action!