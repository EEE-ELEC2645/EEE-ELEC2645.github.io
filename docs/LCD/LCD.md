---
title: LCD
nav_order: 10
layout: default
---

# LCD Screen

In this module we are using a 240 x 240 pixel LCD screen with an SPI interface. This allows us to display graphics and text in our embedded applications. We are using the popular ST7789 driver for this display, specifically the [board from pimoroni](https://shop.pimoroni.com/products/1-54-spi-colour-square-lcd-240x240-breakout?variant=39351811702867).

The LCD and Nucleo Board connections are:

| LCD Pin | Signal  | Nucleo Pin | Purpose |
|---------|---------|-----------|---------|
| VDD     | Power (3.3V-5V) | VDD | Display power supply |
| GND     | Ground  | GND | Ground reference |
| MOSI    | Serial Data | PB15 | SPI Master Output Slave Input |
| SCK     | Clock   | PB13 | SPI Clock signal |
| CS      | Chip Select | PB12 | SPI Chip Select |
| DC      | Data/Command | PB11 | Command vs Data mode |
| BL      | Backlight | PB1 | Backlight control (PWM) |

In this section we will cover how the [LCD library](https://github.com/EEE-ELEC2645/ST7789V2_Driver_STM32L4) works and how to use it in your own projects.
