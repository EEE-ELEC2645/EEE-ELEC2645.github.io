---
title: Timers Introduction
parent: Timers
nav_order: 1
layout: default
---

# Introduction to Timers

Timers are one of the most fundamental peripherals in embedded systems. They allow you to perform time-based operations efficiently without constantly checking the system clock or blocking your program. Let's explore how timing works in embedded systems by starting with the simplest approach and gradually moving to more sophisticated solutions.

## Blinking an LED with HAL_Delay()

The most basic way to create a time delay is using the `HAL_Delay()` function. Here's a simple example of blinking an LED:

```c
while (1) {
    HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);
    HAL_Delay(500);  // Wait 500 milliseconds
}
```

This code toggles an LED every 500ms, creating a 1Hz blink pattern. While this works, it has a **major problem**: `HAL_Delay()` is a **blocking function**. This means:

- Your CPU does nothing but wait during the delay
- No other code can run during this time
- You can't respond to button presses or other events
- Your entire program is "frozen" while waiting

This is fine for very simple programs, but becomes a serious limitation as your project grows.

## A Better Approach: HAL_GetTick()

Instead of blocking, we can use `HAL_GetTick()` to create **non-blocking delays**. This function returns the number of milliseconds since the microcontroller started:

```c
uint32_t lastTime = 0;

while (1) {
    // Check if 500ms has passed
    if (HAL_GetTick() - lastTime >= 500) {
        HAL_GPIO_TogglePin(GPIOA, GPIO_PIN_5);
        lastTime = HAL_GetTick();
    }
    
    // Other code can run here!
    checkButton();
    updateDisplay();
}
```

This is much better! Now our program can do other things while waiting for the time to pass. The CPU isn't blocked, so it can check buttons, update displays, and handle other tasks.

However, there's **still a limitation**: your CPU is constantly checking `HAL_GetTick()` and comparing it to `lastTime`. While this isn't blocking, your processor is still busy running through the loop over and over, consuming power and attention.

## The Hardware Timer Solution

This is where **hardware timers** come in. A timer is a dedicated piece of hardware that counts independently of your CPU. You configure it once, and it counts automatically in the background. When it reaches a certain value, it can:

- Trigger an interrupt (calling a function automatically)
- Toggle a GPIO pin directly (without CPU involvement!)
- Generate PWM signals
- Measure time intervals precisely

The key advantage: **your CPU is free to do other things** while the timer counts in the background. You're not wasting processor cycles constantly checking the time.

When the timer generates an event (for example, when it reaches the period and overflows), the microcontroller can run an **Interrupt Service Routine (ISR)**. An ISR is a special function that the hardware calls automatically. You keep the ISR short and fast: typically set a flag or perform a quick action, then return to your main code.

## How Timers Count

At their core, hardware timers are just counters that increment with each clock tick. But how fast do they count? That depends on two key settings: the **prescaler** and the **period**.

### The Prescaler

The prescaler divides the timer's input clock frequency to slow it down. Think of it like gearing on a bicycle:

- **High prescaler** = slower counting (like an easy gear going uphill)  
- **Low prescaler** = faster counting (like a hard gear for speed)

For example, if your timer is connected to a 48 MHz clock:
- Prescaler = 1: Timer counts at 48 million times per second
- Prescaler = 48: Timer counts at 1 million times per second  
- Prescaler = 48000: Timer counts at 1000 times per second (1 kHz)

The formula is simple:

$$
\text{Timer Frequency} = \frac{\text{Clock Frequency}}{\text{Prescaler}}
$$

### The Period (Auto-Reload Register)

The period determines when the timer "wraps around" and triggers an event. It's also called the **Auto-Reload Register (ARR)**.

The timer counts from 0 up to the period value, then resets to 0 and starts over. Each time it resets, it can trigger an interrupt or other action.

For example, with a timer frequency of 1 kHz:
- Period = 1000: Timer overflows every 1 second
- Period = 500: Timer overflows every 0.5 seconds
- Period = 100: Timer overflows every 0.1 seconds

The formula for the time between events:

$$
\text{Time Interval} = \frac{\text{Period}}{\text{Timer Frequency}}
$$

### Putting It Together

Let's say we want an LED to blink every 500ms, and our timer is connected to an 80 MHz clock:

1. **Prescaler**: 79 (gives us 1 MHz timer frequency: $\frac{80,000,000}{79 + 1} = 1,000,000$ Hz)
2. **ARR (Period)**: 499,999 (for 500ms interval at 2 Hz output frequency)

Let's verify the calculation using the ARR formula:

$$\text{ARR} = \frac{f_{tick}}{f_{output}} - 1 = \frac{1,000,000}{2} - 1 = 499,999$$

**Frequency check:**

$$f_{output} = \frac{1,000,000}{499,999 + 1} = \frac{1,000,000}{500,000} = 2 \text{ Hz}$$

**Period check:**

$$\text{Period} = \frac{1}{2 \text{ Hz}} = 0.5 \text{ seconds} = 500 \text{ ms} \checkmark$$

Now the timer hardware will automatically generate an event every 500ms, and our CPU is completely free to do other work!

## What's Next?

In this introduction, we've covered the fundamental concept of timers and why they're better than software delays. We've also explained the two key parameters—prescaler and period—that control how your timer counts.

The next section provides a **practical example** showing how to implement this in code using STM32 HAL, demonstrating a non-blocking LED blink using timer interrupts. After that, you'll explore:
- Using multiple timers simultaneously
- PWM (Pulse Width Modulation) with timers
- Timer capture mode for measuring input signals
- Advanced timing techniques

Hardware timers unlock a whole new level of efficiency and capability in your embedded projects!
