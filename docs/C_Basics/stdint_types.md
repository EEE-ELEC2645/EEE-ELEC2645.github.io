---
title: Fixed-Width Integer Types (stdint.h)
parent: C Basics
nav_order: 2
layout: default
---

# stdint.h: Fixed-Width Integer Types

## The Problem

Standard C types like `int` and `char` have **size that varies by system**. On one computer, `int` might be 2 bytes. On another, it might be 4 bytes. This is a nightmare for embedded systems like STM32!

```c
// On PC:          int = 4 bytes
// On Arduino:     int = 2 bytes
// On old system:  int = 2 bytes
```

When programming microcontrollers, you **need to know exactly** how big your variables are.

---

## The Solution: `stdint.h`

The header file `<stdint.h>` provides **fixed-width integer types** that are *always* the same size:

| Type | Size | Range |
|------|------|-------|
| `int8_t` | 1 byte | -128 to 127 |
| `uint8_t` | 1 byte | 0 to 255 |
| `int16_t` | 2 bytes | -32,768 to 32,767 |
| `uint16_t` | 2 bytes | 0 to 65,535 |
| `int32_t` | 4 bytes | -2.1 billion to 2.1 billion |
| `uint32_t` | 4 bytes | 0 to 4.2 billion |

**Key:** The number in the type name (`8`, `16`, `32`) tells you the exact bit width.

---

## Why STM32 HAL Uses These

When you work with the STM32 HAL, you'll see types like:

```c
uint8_t button_state;      // GPIO pin value (0 or 1)
uint16_t adc_reading;      // 12-bit ADC result
uint32_t tick_count;       // Timer counter
```

### Why?

1. **Hardware registers are fixed-size** - The microcontroller's memory-mapped registers are 8, 16, or 32 bits. You need types that match exactly.

2. **Portability** - If you port code from STM32L476 to STM32F4, the types stay consistent.

3. **No surprises** - `uint8_t` is *always* 1 byte.

---

## Common Examples in STM32

```c
#include "stdint.h"

// GPIO: Reading a pin (0 or 1)
uint8_t pin_state = HAL_GPIO_ReadPin(GPIOB, GPIO_PIN_2);

// ADC: Reading an analog value (12-bit: 0-4095)
uint16_t adc_value = HAL_ADC_GetValue(&hadc1);

// Timer: Getting elapsed time
uint32_t time_ms = HAL_GetTick();

// SPI/UART: Data bytes
uint8_t buffer[64];
HAL_UART_Receive(&huart2, buffer, 64, 1000);
```

---

## Quick Reference

**Use these in your code:**

- `uint8_t` - Single byte (0-255) - **Most common for GPIO, SPI, UART**
- `uint16_t` - Two bytes (0-65535) - ADC values, timer counts
- `uint32_t` - Four bytes - Large counts, addresses
- `int8_t`, `int16_t`, `int32_t` - When you need negative numbers

**Avoid:**

- Plain `int` - Size varies by system
- `char` - Ambiguous (is it a character or a number?)

---

## Summary

**In embedded systems, always use `stdint.h` types.** They guarantee the exact size you need, which prevents mysterious bugs when your code moves to a different microcontroller.

Most HAL libraries already include `stdint.h`, so you get these types automatically.
