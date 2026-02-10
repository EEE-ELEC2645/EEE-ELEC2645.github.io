---
title: PWM
nav_order: 13
layout: default
---

# Pulse Width Modulation (PWM)

Timers are useful for many things, but one of their most common applications is **Pulse Width Modulation (PWM)**. PWM allows you to control the power delivered to devices like LEDs, motors, and buzzers by rapidly switching them on and off at a high frequency. The "width" of the "pulse" (the amount of time it's on vs off) determines how much power the device receives.

Compared to the timer triggered interrupts we covered in the Timers section, PWM is a continuous signal that can be adjusted on the fly, typically with a higher frequency (e.g., 1 kHz or more) to avoid flickering in LEDs or audible noise in motors.
