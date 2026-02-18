---
title: FSM Introduction
parent: Finite State Machines (FSM)
nav_order: 1
layout: default
---

# Introduction to Finite State Machines

Finite State Machines (FSMs) are a simple way to structure embedded code when your system has a small number of distinct modes. Instead of scattering logic across many `if` statements, you define states, decide events that cause transitions, and keep the behaviour in each state clear and predictable.

## Why Use FSMs in Embedded Systems?

FSMs are great for embedded applications because they make your code:

- Predictable: each state has a clear set of allowed transitions
- Readable: you can see all the modes in one place
- Testable: you can test each state and transition independently
- Scalable: adding a new state is usually a small change

This is exactly the kind of structure used in the course FSM lab: [https://github.com/EEE-ELEC2645/Unit_3_4_FSM](https://github.com/EEE-ELEC2645/Unit_3_4_FSM)

## The Core Idea

An FSM is defined by:

1. States: the modes your system can be in
2. Events: inputs or conditions that cause a change
3. Transitions: rules for moving between states
4. Actions: what your system does while in each state

Think of a simple LED controller with three modes:

| State | Description | Event | Next State |
|------|-------------|-------|------------|
| OFF | LED is off | Button press | ON |
| ON | LED is on | Button press | BLINK |
| BLINK | LED blinks | Button press | OFF |

## A Minimal FSM Example

This example shows the basic structure. The button press triggers state changes, and each state has its own behaviour.

```c
typedef enum {
    STATE_OFF,
    STATE_ON,
    STATE_BLINK
} LedState;

static LedState current_state = STATE_OFF;
static uint8_t button_event = 0;  // Set to 1 in an EXTI callback

void fsm_update(void)
{
    // Handle transitions first
    if (button_event) {
        button_event = 0;
        if (current_state == STATE_OFF) {
            current_state = STATE_ON;
        } else if (current_state == STATE_ON) {
            current_state = STATE_BLINK;
        } else {
            current_state = STATE_OFF;
        }
    }

    // State actions
    if (current_state == STATE_OFF) {
        led_off();
    } else if (current_state == STATE_ON) {
        led_on();
    } else {
        blink_led_nonblocking();
    }
}
```

## Practical Notes for This Course

- Use an enum for states so they are easy to read and debug.
- Keep transitions separate from actions. This makes changes safer.
- Trigger transitions using interrupt-driven events (for example, set a flag in the EXTI callback, process it in `main`).
- If a state needs timing (like blink), use a timer interrupt or `HAL_GetTick()` instead of `HAL_Delay()`.

## Where This Fits Next

In the FSM lab, you will build a small state machine that responds to inputs and updates outputs in a structured way. It brings together the ideas from GPIO, interrupts, and timers into a single, clean program structure.
