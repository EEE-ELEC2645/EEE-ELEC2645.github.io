---
title: Interrupts Introduction
parent: Interrupts
nav_order: 1
layout: default
math: katex
---

# Introduction to Interrupts

Interrupts are one of the most powerful features in embedded systems. They allow your microcontroller to respond immediately to external events—like button presses, sensor readings, or timer overflows—without constantly checking (polling) whether those events have occurred. This makes your programs more responsive, more efficient, and more power-friendly.

## The Problem: Polling vs Interrupts

### Traditional Polling Approach

Without interrupts, if you want to respond to a button press, you need to constantly check the button state in your main loop:

```c
while (1) {
    if (button_is_pressed()) {
        do_something();
    }
    if (timer_expired()) {
        do_something_else();
    }
    // More checks...
}
```

**Whats so wrong with that?**
- **Wasted CPU cycles**: The processor is constantly checking conditions, even when nothing is happening
- **Slower response time**: You only detect events during your check, which could miss quick events
- **Power consumption**: Constant checking keeps the CPU busy and prevents low-power sleep modes
- **Complex timing**: Managing multiple time-sensitive tasks becomes difficult, what if you need to check 10 buttons and a timer? Your loop becomes a mess of checks and delays.

### Interrupt-Driven Approach

With interrupts, the hardware automatically pauses your program when an event occurs and executes a special function called an **Interrupt Service Routine (ISR)**:

```c
// Main loop can do nothing or perform low-priority tasks
while (1) {
    // Maybe update a display or process data
    // Or just sleep to save power
    __WFI();  // Wait for interrupt
}

// Hardware automatically calls this when button is pressed
void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin) {
    // Respond immediately!
    if (GPIO_Pin == BUTTON_Pin) {
        toggle_led();
    }
}
```

**Advantages of interrupts:**
- **Immediate response**: Hardware detects the event instantly (typically within microseconds)
- **Efficient CPU use**: The main loop can perform other tasks or sleep
- **Lower power**: CPU can enter low-power modes between interrupts
- **Cleaner code**: Event handlers are separated from main logic

## How Interrupts Work

When an interrupt occurs, the following sequence happens automatically:

1. **Event occurs** (button pressed, timer expires, data received, etc.)
2. **Hardware detects the event** and signals the processor
3. **Current execution pauses** (the processor saves its state)
4. **ISR executes** (your interrupt handler function runs)
5. **Original execution resumes** (processor restores state and continues)

All of this happens in microseconds, and from your main program's perspective, it's nearly invisible—except for the work that the ISR performed.

### Interrupt Priority

Not all interrupts are equally important. The STM32 microcontroller has a priority system:

- **Higher priority** interrupts can interrupt lower priority ones
- **Equal priority** interrupts are handled in a fixed order
- **Non-interruptible** periods exist to maintain system stability

This allows critical events (like hardware faults) to interrupt less critical ones (like button presses).

## External Interrupts (EXTI)

External interrupts are triggered by GPIO pins changing state. This is perfect for handling button presses, sensor signals, or any external digital event. In STM32 HAL, these are called **EXTI (External Interrupt)** lines.

### How EXTI Works

Each GPIO pin can be configured to trigger an interrupt on:
- **Rising edge**: 0 → 1 transition
- **Falling edge**: 1 → 0 transition  
- **Both edges**: Any change

When you press a button (or any GPIO event occurs), the hardware:
1. Detects the specified edge on the pin
2. Triggers the corresponding EXTI interrupt
3. Calls your callback function with the pin number

### Example: Button Interrupt Handler

```c
/**
 * @brief  EXTI line detection callback
 * @param  GPIO_Pin: The pin that triggered the interrupt
 * @note   This function is called by hardware when any GPIO interrupt fires.
 *         You check GPIO_Pin to determine which button was pressed.
 */
void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
{
    if (GPIO_Pin == BTN2_Pin) {
        // Button 2 was pressed - toggle LED
        HAL_GPIO_TogglePin(LED_GPIO_Port, LED_Pin);
    }
    
    if (GPIO_Pin == BTN3_Pin) {
        // Button 3 was pressed - change mode
        mode = (mode + 1) % 3;
    }
}
```

The key point: **one callback handles all GPIO interrupts**. You use the `GPIO_Pin` parameter to determine which pin triggered the interrupt.

## Software Debouncing in ISRs

Physical buttons are mechanical devices that "bounce"—they make and break contact multiple times when pressed. This can trigger multiple interrupts from a single press. To prevent this, we use **software debouncing**:

```c
// Global variables to track last interrupt time for each button
uint32_t btn2_last_interrupt_time = 0;
uint32_t btn3_last_interrupt_time = 0;

void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
{
    uint32_t current_time = HAL_GetTick();
    
    if (GPIO_Pin == BTN2_Pin) {
        // Ignore interrupts that occur within DEBOUNCE_DELAY ms
        if ((current_time - btn2_last_interrupt_time) > DEBOUNCE_DELAY) {
            btn2_last_interrupt_time = current_time;
            
            // Handle button press
            toggle_led();
        }
    }
    
    if (GPIO_Pin == BTN3_Pin) {
        if ((current_time - btn3_last_interrupt_time) > DEBOUNCE_DELAY) {
            btn3_last_interrupt_time = current_time;
            
            // Handle button press
            change_mode();
        }
    }
}
```

We maintain separate timestamp variables for each button to track the last time each button triggered an interrupt. We only act on the button press if enough time has passed since the last one.

## Timer Interrupts

Like GPIO interrupts, timers can also trigger interrupts when they reach their period (see the [Timers Introduction](../timers/timers_intro.md) for more on how timers count). This is more efficient than polling `HAL_GetTick()` in a loop.

### Timer Interrupt Callback

```c
void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
    if (htim->Instance == TIM6) {
        // Timer 6 has elapsed - set a flag or perform quick action
        g_timer_flag = 1;
    }
}
```

Timer interrupts are covered in detail in the [Timers HAL section](../timers/timers_HAL.md).

## Best Practices for ISRs

**ISRs should be fast and simple:**

1. **Keep them short**: Long ISRs delay other interrupts and slow overall system response
2. **Set flags, don't process**: Set a flag in the ISR and process data in the main loop
3. **Avoid blocking operations**: No `HAL_Delay()`, `printf()`, or complex calculations
4. **Use volatile for shared variables**: Any variable accessed by both ISR and main code must be `volatile`

### Good ISR Pattern

```c
// Shared flag - volatile because ISR writes, main reads
volatile uint8_t button_pressed = 0;

void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
{
    if (GPIO_Pin == BTN2_Pin) {
        button_pressed = 1;  // Just set a flag
    }
}

// In main loop:
while (1) {
    if (button_pressed) {
        button_pressed = 0;
        
        // Do complex processing here, not in ISR
        processButtonPress();
        updateDisplay();
    }
}
```

## Event-Driven Programming

Interrupts enable **event-driven programming**: your program waits for events rather than constantly checking for them. This is the architecture used in many real-time systems, user interfaces, and communication protocols.

```c
int main(void)
{
    // Initialize hardware
    SystemClock_Config();
    MX_GPIO_Init();
    MX_TIM6_Init();
    
    // Start timer with interrupts
    HAL_TIM_Base_Start_IT(&htim6);
    
    // Event-driven main loop
    while (1) {
        // Main loop does nothing - all work happens in ISRs
        // Or put MCU to sleep to save power
        __WFI();  // Wait for interrupt
    }
}
```

This approach is demonstrated in the course's [event-driven programming example](https://github.com/EEE-ELEC2645/Unit_3_4_Event_Trigger), where button presses immediately trigger LED changes and display updates without any polling.
