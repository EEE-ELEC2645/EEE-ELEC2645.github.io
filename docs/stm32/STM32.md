---
title: STM32
nav_order: 9
layout: default
---

# STM32 Microcontrollers

Our C programming enters the real world! STM32 microcontrollers are widely used in embedded systems. This section introduces the STM32 Nucleo development boards, and programming using the STM32 HAL (Hardware Abstraction Layer) library.

## Everything is a Peripheral

One key concept in microcontroller programming: **everything on the STM32 is a peripheral**.

```
Your Microcontroller
┌────────────────────────────────────────┐
│                                        │
│  GPIO  │  UART  │  SPI  │  Timer       │  LEDs, buttons, sensors
│  ──────┼────────┼───────┼─────         │  Serial communication
│  ADC   │  DAC   │  I2C  │  USB         │  Analog measurement, PWM
│        │        │       │              │  Power management, clocks
└────────────────────────────────────────┘
```

**GPIO (General Purpose I/O)** - Digital pins for reading and writing signals  
**UART** - Serial communication (printing to console)  
**SPI** - Fast communication for LCD, sensors  
**I2C** - Communication for devices like temperature sensors  
**Timers** - Counting pulses, creating PWM signals  
**ADC** - Converting analog voltages to digital values  

Each peripheral is controlled through **registers** (special memory locations). The HAL library hides this complexity, so you call simple functions like `HAL_GPIO_WritePin()` instead of manipulating registers directly.

**What to keep in mind:** When programming the STM32, think of the microcontroller as a collection of tools. To use a tool (blink an LED, read a sensor), you configure its peripheral and call HAL functions. Every interaction with the hardware goes through a peripheral.

