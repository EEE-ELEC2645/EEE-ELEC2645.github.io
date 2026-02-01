---
title: Joystick Library Reference
parent: Joystick
nav_order: 2
layout: default
---


# Joystick Library API Reference

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

Complete reference for all functions, structures, and constants in the joystick library.

## Constants

### `JOYSTICK_DEFAULT_CENTER_X`
Default center position for X-axis (12-bit ADC: 0-4095, midpoint=2048)

### `JOYSTICK_DEFAULT_CENTER_Y`
Default center position for Y-axis (12-bit ADC: 0-4095, midpoint=2048)

### `JOYSTICK_DEADZONE`
Default deadzone radius around centre (in ADC units, ~5% of max range). Default value: 200

### `JOYSTICK_MAX_VALUE`
Maximum 12-bit ADC value (used for normalisation): 4095

## Data Structures

### `Direction` (enum)
8-way directional output plus centre position:
- `CENTRE` - Joystick at neutral position
- `N` - North (up)
- `NE` - North-East
- `E` - East (right)
- `SE` - South-East
- `S` - South (down)
- `SW` - South-West
- `W` - West (left)
- `NW` - North-West

### `Vector2D` (struct)
Cartesian coordinates for joystick position.

**Fields:**
- `float x` - X-axis coordinate (-1.0 left to +1.0 right)
- `float y` - Y-axis coordinate (-1.0 down to +1.0 up)

### `Polar` (struct)
Polar coordinates from circle-mapped joystick position.

**Fields:**
- `float mag` - Magnitude 0.0 → 1.0 (from circle-mapped coordinates), or 0 if centred
- `float angle` - Angle 0-360° (compass: 0°=North, 90°=East), or -1 if centred

### `UserInput` (struct)
High-level joystick input representation for game/application logic.

**Fields:**
- `Direction direction` - 8-direction enum (N, NE, E, SE, S, SW, W, NW, CENTRE)
- `float magnitude` - Magnitude 0.0 → 1.0 (from circle-mapped coordinates)
- `float angle` - Raw angle 0-360° for finer control, or -1 if centred

### `Joystick_cfg_t` (struct)
Configuration for a joystick instance. Holds all static configuration that never changes after initialisation.

**Fields:**
- `ADC_HandleTypeDef* adc` - Pointer to ADC handle (e.g., &hadc1)
- `uint32_t x_channel` - ADC channel for X-axis (e.g., ADC_CHANNEL_1)
- `uint32_t y_channel` - ADC channel for Y-axis (e.g., ADC_CHANNEL_2)
- `uint32_t sampling_time` - ADC sampling time (e.g., ADC_SAMPLETIME_2CYCLES_5)
- `uint16_t center_x` - Calibrated centre ADC value for X (typically ~2048 for 12-bit)
- `uint16_t center_y` - Calibrated centre ADC value for Y (typically ~2048 for 12-bit)
- `uint16_t deadzone` - Deadzone around centre in ADC units (e.g., 200)
- `uint8_t setup_done` - Internal flag: 1 if initialised, 0 otherwise
- `ADC_ChannelConfTypeDef adc_config` - Cached ADC channel configuration (set during Init)

### `Joystick_t` (struct)
Current joystick state with all coordinate representations. Populated by `Joystick_Read()`.

**Fields:**
- `uint16_t x_raw` - Raw ADC value for X-axis (0-4095 for 12-bit ADC)
- `uint16_t y_raw` - Raw ADC value for Y-axis (0-4095 for 12-bit ADC)
- `int16_t x_processed` - Centred X with deadzone applied (-2048 to +2048)
- `int16_t y_processed` - Centred Y with deadzone applied (-2048 to +2048)
- `Vector2D coord` - Normalised Cartesian coords (-1.0 to 1.0) before circle mapping
- `Vector2D coord_mapped` - Circle-mapped coordinates (-1.0 to 1.0) for uniform control
- `float angle` - Angle 0-360° from circle-mapped coords, or -1 if centred
- `Direction direction` - Discrete 8-direction output
- `float magnitude` - Magnitude 0.0→1.0 from circle-mapped coords

## Core Functions

### `Joystick_Init()`
```c
void Joystick_Init(Joystick_cfg_t* cfg);
```

Initialise joystick with ADC configuration.

**Parameters:**
- `cfg` - Pointer to joystick configuration struct

**Description:**  
Performs one-time ADC setup including configuring ADC channels and sampling time, building and caching the ADC configuration struct for efficient channel switching, and setting the setup_done flag to prevent duplicate initialisation.

Call this once during system initialisation before using `Joystick_Read()`. After Init, use `Joystick_Calibrate()` to find centre position.

**Note:** Must be called exactly once per joystick configuration.

### `Joystick_Calibrate()`
```c
void Joystick_Calibrate(Joystick_cfg_t* cfg);
```

Calibrate joystick centre position.

**Parameters:**
- `cfg` - Pointer to joystick configuration struct

**Description:**  
Reads 50 ADC samples and averages them to find the neutral centre position. Stores results in `cfg->center_x` and `cfg->center_y`.

Should be called after `Joystick_Init()` whilst joystick is held in neutral position. This calibration compensates for manufacturing variations and slight mechanical offsets.

**Note:** Blocking operation (~500ms for 50 samples).

### `Joystick_Read()`
```c
void Joystick_Read(Joystick_cfg_t* cfg, Joystick_t* data);
```

Read joystick and compute all coordinate representations.

**Parameters:**
- `cfg` - Pointer to joystick configuration struct
- `data` - Pointer to joystick data struct to be populated

**Description:**  
Performs complete joystick processing:
- Reads raw ADC values for both X and Y axes
- Applies deadzone around centre (values within deadzone → 0)
- Normalises to Cartesian coordinates (-1.0 to 1.0)
- Applies circle mapping for uniform control feel
- Calculates polar coordinates (magnitude, angle)
- Determines 8-direction discrete output

All fields in data struct are populated. Call this in your main loop to update joystick state before reading individual fields.

**Note:** Non-blocking operation (~200μs for two ADC conversions).

## Data Retrieval Functions

### `Joystick_GetInput()`
```c
UserInput Joystick_GetInput(Joystick_t* data);
```

Get high-level joystick input (direction, magnitude, angle).

**Parameters:**
- `data` - Pointer to populated joystick data struct

**Returns:**  
`UserInput` struct with direction enum, magnitude, and angle.

**Description:**  
Extracts the most commonly used joystick values:
- `direction` - 8-way discrete output (useful for game controls)
- `magnitude` - 0.0→1.0 from circle-mapped coords (useful for speed control)
- `angle` - 0-360° for finer directional control (or -1 if centred)

This is the primary interface for most applications. Call after `Joystick_Read()`.

### `Joystick_GetPolar()`
```c
Polar Joystick_GetPolar(Joystick_t* data);
```

Get full polar coordinates from circle-mapped position.

**Parameters:**
- `data` - Pointer to populated joystick data struct

**Returns:**  
`Polar` struct with magnitude and angle.

**Description:**  
Returns the polar representation computed during `Joystick_Read()`. Same data as `Joystick_GetInput()` polar fields, but in dedicated struct.

- Magnitude: 0.0→1.0, where 1.0 is maximum deflection
- Angle: 0-360°, where 0°=North, 90°=East, etc. (or -1 if centred)

## Helper Functions

### `Joystick_GetCoord()`
```c
Vector2D Joystick_GetCoord(int16_t x, int16_t y, uint16_t center_x, uint16_t center_y);
```

Normalise raw ADC readings to Cartesian coordinates.

**Parameters:**
- `x` - Raw ADC value for X-axis
- `y` - Raw ADC value for Y-axis
- `center_x` - Calibrated centre position for X
- `center_y` - Calibrated centre position for Y

**Returns:**  
`Vector2D` with normalised coordinates (-1.0 to 1.0).

**Description:**  
Subtracts centre and scales to [-1.0, 1.0] range based on `JOYSTICK_MAX_VALUE`. Used internally by `Joystick_Read()` to normalise coordinates.

### `Joystick_MapToCircle()`
```c
Vector2D Joystick_MapToCircle(Vector2D coord);
```

Apply circle mapping transformation.

**Parameters:**
- `coord` - Cartesian coordinates (-1.0 to 1.0)

**Returns:**  
Circle-mapped coordinates (-1.0 to 1.0).

**Description:**  
Transforms square input range to circular output for uniform control feel. Algorithm: x' = x*sqrt(1 - y²/2), y' = y*sqrt(1 - x²/2). This ensures equal force is needed in all directions and maximises effective range.

**Reference:** [http://mathproofs.blogspot.co.uk/2005/07/mapping-square-to-circle.html](http://mathproofs.blogspot.co.uk/2005/07/mapping-square-to-circle.html)

### `Joystick_GetDirection()`
```c
Direction Joystick_GetDirection(float angle, float magnitude);
```

Get 8-direction output from angle and magnitude.

**Parameters:**
- `angle` - Raw angle in degrees (0-360°, or -1 for centred)
- `magnitude` - Magnitude 0.0→1.0

**Returns:**  
`Direction` enum (N, NE, E, SE, S, SW, W, NW, or CENTRE).

**Description:**  
Maps continuous angle to discrete 8-direction output. Compass orientation: 0°=N, 45°=NE, 90°=E, 135°=SE, etc. Returns CENTRE if angle is invalid or magnitude < 0.05 (within deadzone).

Used internally by `Joystick_Read()` but available for custom angle inputs.

## Usage Example

```c
// Configuration
Joystick_cfg_t joy_cfg = {
    .adc = &hadc1,
    .x_channel = ADC_CHANNEL_1,
    .y_channel = ADC_CHANNEL_2,
    .sampling_time = ADC_SAMPLETIME_2CYCLES_5,
    .center_x = JOYSTICK_DEFAULT_CENTER_X,
    .center_y = JOYSTICK_DEFAULT_CENTER_Y,
    .deadzone = JOYSTICK_DEADZONE,
    .setup_done = 0
};

Joystick_t joy_data;

// Initialisation
Joystick_Init(&joy_cfg);
Joystick_Calibrate(&joy_cfg);

// Main loop
while (1) {
    Joystick_Read(&joy_cfg, &joy_data);
    
    // Option 1: Get high-level input
    UserInput input = Joystick_GetInput(&joy_data);
    if (input.direction == N) {
        // Move north
    }
    
    // Option 2: Use Cartesian coordinates
    float x = joy_data.coord.x;
    float y = joy_data.coord.y;
    
    // Option 3: Use polar coordinates
    Polar p = Joystick_GetPolar(&joy_data);
    if (p.mag > 0.5f) {
        // Strong deflection
    }
}
```
