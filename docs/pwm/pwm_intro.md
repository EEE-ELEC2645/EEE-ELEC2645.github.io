---
title: PWM Introduction
parent: PWM
nav_order: 1
layout: default
math: katex
---

# Introduction to PWM

Pulse Width Modulation (PWM) is one of the most useful and versatile features of microcontrollers. It allows you to control the power delivered to a device by rapidly switching between ON and OFF states. This simple concept enables loads of interesting applications: dimming LEDs, controlling motor speeds, generating analog signals from digital outputs, and even creating audio.

The important thing to remember is this: **if you switch something on and off fast enough, the average power delivered depends on how long it stays ON versus OFF**.

## Why Not Just Use GPIO?

You might wonder: "Why can't I just toggle a GPIO pin on and off to control an LED's brightness?" Technically, you could, but it comes with serious problems:

```c
// Manual "bit-banging" approach (NOT RECOMMENDED)
while (1) {
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET);    // ON
    HAL_Delay(25);
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_RESET);  // OFF
    HAL_Delay(75);
}
```

The problems are:

- **Your CPU is blocked** during delays, preventing other tasks
- **Precision suffers** because `HAL_Delay()` isn't exact as it can be affected by interrupts and other code
- **No responsiveness** to events like button presses while timing
- **Inflexible frequencies** – changing speed requires code changes
- **Power inefficient** – the CPU is active the whole time, consuming more power

## The Hardware PWM Solution

This is exactly why hardware PWM exists. Just like **hardware timers** run independently in the background, **PWM uses timers to generate the switching signal automatically**. You configure it once, and the hardware does the rest while your CPU continues running other code.

Here's what a PWM signal looks like:

```
Period
|<---------->|
         ___        ___        ___    
    ____|   |______|   |______|   |___
Duty   ^    ^
Cycle  |    |
```

A PWM signal is characterized by two parameters:

1. **Frequency (Period)**: How fast the signal switches on and off
2. **Duty Cycle**: The percentage of time the signal stays HIGH

For example:
- **50% duty cycle** = signal is ON half the time, OFF half the time = 50% average power
- **25% duty cycle** = signal is ON for 1/4 of the period = 25% average power
- **100% duty cycle** = signal is always ON = full power

## Practical Applications

PWM is used everywhere in embedded systems:

### LED Brightness Control
Instead of just ON/OFF, you can smoothly dim an LED by adjusting the duty cycle. 10% brightness = 10% duty cycle, 50% brightness = 50% duty cycle, etc.

### Motor Speed Control
Motors respond to average voltage. By controlling the duty cycle of a PWM signal, you can make a motor run at any speed from stopped to full speed.

### Servo Control
Servo motors interpret PWM pulse widths as position commands. Different pulse widths move the servo to different angles.

### Audio and Signal Generation
PWM signals can be filtered with capacitors and resistors to generate analog voltage levels, allowing you to create audio or control analog circuits from a digital microcontroller.

## How PWM Uses Hardware Timers

Remember from the timers introduction that a hardware timer counts from 0 to its **period value**, then resets and triggers an interrupt. PWM extends this idea:

The timer can automatically toggle a GPIO pin when the counter reaches a specific value (the **compare value**). This happens without any CPU involvement, or without the need for an ISR. The timer handles the switching, and you just set the parameters:

1. CPU configures: "Toggle pin when counter reaches 25% of the period"
2. Timer counts from 0 to 100% of period, toggling the pin at 25%
3. Timer resets and repeats
4. **CPU is completely free** to do other things

The **duty cycle** is simply the ratio of when the pin gets toggled:

$$
\text{Duty Cycle (\%)} = \frac{\text{Compare Value}}{\text{Period}} \times 100
$$

## Key PWM Parameters

When you configure PWM on an STM32 microcontroller, you'll set these parameters:

| Parameter | Meaning | Example |
|-----------|---------|---------|
| **Frequency** | How fast PWM switches (Hz or kHz) | 1000 Hz = 1 millisecond period |
| **Period** | Timer counts from 0 to this value | 80 (with 1 MHz timer = 80 µs period) |
| **Prescaler** | Clock divider (from timer setup) | 80 (divides 80 MHz clock by 80) |
| **Pulse/Compare** | When to toggle during the period | 20 (means 20/80 = 25% duty cycle) |

## What's Next?

Now that you understand **what** PWM does and **why** it's useful, the next steps are:

1. Learn how to **configure PWM** on STM32 microcontrollers using HAL
2. Understand **how to change duty cycle and frequency** in code
3. Explore practical applications like **LED dimming** and **motor control**
4. Discover specialized PWM modes for specific applications

PWM transforms what would be a tedious, CPU-intensive task into something the hardware handles automatically. Let's get started implementing it!
