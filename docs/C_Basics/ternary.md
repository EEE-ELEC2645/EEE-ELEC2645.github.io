---
title: Ternary Operator
parent: C Basics
nav_order: 14
layout: default
---

# Short Hand If Statements:

The Ternary Operator (`?:`)

## Introduction

The **ternary operator** is a compact way to write simple `if-else` statements in a single line. It's called "ternary" because it takes three operands: a condition, a value if true, and a value if false.

```c
result = condition ? value_if_true : value_if_false;
```

This is especially useful for toggling values, selecting between two options, or assigning values conditionally - common tasks in embedded systems like controlling LEDs.

---

## Why use the ternary operator?

It's a concise way to assign values or toggle states based on simple conditions. Great for LED control and straightforward true/false choices, but stick to `if` statements for complex logic.

---

## Syntax

```c
condition ? expression_if_true : expression_if_false
```

**How it works:**

1. The `condition` is evaluated
2. If it's **true** (non-zero), the operator returns `expression_if_true`
3. If it's **false** (zero), the operator returns `expression_if_false`

---

## Basic Example

```c
int x = 10;
int y = 20;

// Find the maximum value
int max = (x > y) ? x : y;  // max = 20
```

This is equivalent to:

```c
int max;
if (x > y) {
    max = x;
} else {
    max = y;
}
```

The ternary version is more compact and just as readable.

---

## Toggling an LED

One of the most common uses in embedded systems is **toggling a state**, like turning an LED on or off.

### Example 1: Conditional Toggle Based on Input

```c
#include <stdbool.h>
#include <stdint.h>

bool led_state = false;
uint8_t button_pressed = 1;  // 1 = pressed, 0 = not pressed

// Toggle LED only if button is pressed
led_state = button_pressed ? !led_state : led_state;
```

### Example 2: LED Control with GPIO

A more realistic embedded example using GPIO pins:

```c
#include <stdbool.h>
#include "stm32f4xx_hal.h"  // Example for STM32

bool led_on = false;

void toggle_led(void) {
    // Toggle the state
    led_on = !led_on;
    
    // Set GPIO pin based on state
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, 
                      led_on ? GPIO_PIN_SET : GPIO_PIN_RESET);
}
```

---

Use it when it makes code **clearer**, not just shorter. For complex logic or multiple statements, stick with traditional `if-else` blocks.

**Remember:** Clear code is better than clever code. The ternary operator should make your intent obvious, not obscure it! 
