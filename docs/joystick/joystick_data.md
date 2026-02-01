---
title: Joystick Data Processing Pipeline
parent: Joystick
nav_order: 1
layout: default
---

# Joystick Data Processing Pipeline

This guide explains the complete data flow from raw ADC readings to usable coordinate systems and directional output.

## Overview

The joystick library processes raw analogue inputs through several stages to provide multiple representations of the same physical input. This allows you to choose the most appropriate format for your application.

```
Raw ADC Values → Centre & Deadzone → Normalisation → Circle Mapping → Polar Conversion → Direction
   (0-4095)         (filtered)        (-1.0 to 1.0)    (uniform feel)   (mag, angle)     (8-way)
```

## Stage 1: Raw ADC Reading

The joystick has two potentiometers (one for each axis) connected to ADC channels. When you call `Joystick_Read()`, the library reads both channels:

```c
// Read X-axis
cfg->adc_config.Channel = cfg->x_channel;
HAL_ADC_ConfigChannel(cfg->adc, &cfg->adc_config);
HAL_ADC_Start(cfg->adc);
HAL_ADC_PollForConversion(cfg->adc, HAL_MAX_DELAY);
data->x_raw = HAL_ADC_GetValue(cfg->adc);  // 0 to 4095
HAL_ADC_Stop(cfg->adc);

// Read Y-axis (same process)
```

**Output:**
- `data->x_raw`: 0 to 4095 (12-bit ADC)
- `data->y_raw`: 0 to 4095 (12-bit ADC)

**Typical Values:**
- Joystick fully left: x_raw ≈ 0
- Joystick centred: x_raw ≈ 2048
- Joystick fully right: x_raw ≈ 4095

## Stage 2: Centre and Deadzone Processing

Raw ADC values are centred around the calibrated neutral position and filtered through a deadzone to eliminate noise and drift.

```c
// Centre the values
data->x_processed = data->x_raw - cfg->center_x;  // Now ranges from ~-2048 to +2048
data->y_processed = data->y_raw - cfg->center_y;

// Apply deadzone (default: 200 ADC units)
if (abs(data->x_processed) < cfg->deadzone) {
    data->x_processed = 0;
}
if (abs(data->y_processed) < cfg->deadzone) {
    data->y_processed = 0;
}
```

**Purpose:**
- Remove manufacturing variations (no two joysticks have exactly the same centre)
- Filter out small unintentional movements
- Create a "dead" zone where the joystick is considered centred

**Output:**
- `data->x_processed`: -2048 to +2048 (0 if within deadzone)
- `data->y_processed`: -2048 to +2048 (0 if within deadzone)

**Example:**
If centre_x = 2045 and deadzone = 200:
- Raw value 2045: processed = 0 (within deadzone)
- Raw value 2150: processed = 0 (within deadzone, since |105| < 200)
- Raw value 2300: processed = 255 (outside deadzone)

## Stage 3: Normalisation to Cartesian Coordinates

The processed values are normalised to the range [-1.0, 1.0] for easier mathematical operations.

```c
Vector2D Joystick_GetCoord(int16_t x, int16_t y, uint16_t center_x, uint16_t center_y)
{
    // Normalise to -1.0 to 1.0 range
    float norm_x = (float)x / (float)center_x;
    float norm_y = (float)y / (float)center_y;
    
    // Clamp to prevent exceeding range
    if (norm_x > 1.0f) norm_x = 1.0f;
    if (norm_x < -1.0f) norm_x = -1.0f;
    if (norm_y > 1.0f) norm_y = 1.0f;
    if (norm_y < -1.0f) norm_y = -1.0f;
    
    // Note: Y is negated so positive is up
    Vector2D coord = {norm_x, -norm_y};
    return coord;
}
```

**Coordinate System:**
- +X points East (right): -1.0 (left) to +1.0 (right)
- +Y points North (up): -1.0 (down) to +1.0 (up)

**Output:**
- `data->coord.x`: -1.0 to 1.0
- `data->coord.y`: -1.0 to 1.0

**Example Positions:**
- Centre: (0.0, 0.0)
- North (up): (0.0, 1.0)
- East (right): (1.0, 0.0)
- North-East: (0.707, 0.707) ← Note the magnitude!

## Stage 4: Circle Mapping

At this point, there's a problem: diagonal positions have a magnitude of √2 ≈ 1.414, whilst cardinal directions have magnitude 1.0. This means:
- Moving the joystick to North-East corner requires more force
- The effective range is limited by the cardinal directions

Circle mapping solves this by transforming the square input range [-1, 1] × [-1, 1] into a circular output range.

```c
Vector2D Joystick_MapToCircle(Vector2D coord)
{
    float x = coord.x * sqrtf(1.0f - (coord.y * coord.y) / 2.0f);
    float y = coord.y * sqrtf(1.0f - (coord.x * coord.x) / 2.0f);
    
    Vector2D mapped = {x, y};
    return mapped;
}
```

**Mathematical Formula:**
- x' = x × √(1 - y²/2)
- y' = y × √(1 - x²/2)

**Effect:**
This transformation stretches the corners of the square outward to meet the unit circle, whilst preserving the cardinal directions.

**Before and After:**

| Position | Before Mapping | After Mapping | Magnitude Change |
|----------|---------------|---------------|------------------|
| North (0, 1) | (0.0, 1.0) | (0.0, 1.0) | 1.0 → 1.0 (unchanged) |
| East (1, 0) | (1.0, 0.0) | (1.0, 0.0) | 1.0 → 1.0 (unchanged) |
| NE Corner (1, 1) | (0.707, 0.707) | (0.835, 0.835) | 1.0 → 1.18 |
| Full Diagonal (1, 1) | (1.0, 1.0) | (0.707, 0.707) | 1.414 → 1.0 |

**Output:**
- `data->coord_mapped.x`: -1.0 to 1.0
- `data->coord_mapped.y`: -1.0 to 1.0

**Benefit:**
Now all directions feel equal when using magnitude for speed control. A player doesn't get an advantage by moving diagonally.

**Reference:** http://mathproofs.blogspot.co.uk/2005/07/mapping-square-to-circle.html

## Stage 5: Polar Coordinate Conversion

For many applications (especially games), it's more natural to think in terms of "how far" (magnitude) and "which direction" (angle) rather than x and y coordinates.

```c
Polar Joystick_GetPolar(Joystick_t* data)
{
    Polar p;
    
    // Swap axes to convert from mathematical angle to compass heading
    // Mathematical: 0° = East (x-axis)
    // Compass: 0° = North (y-axis)
    float x = data->coord_mapped.y;
    float y = data->coord_mapped.x;
    
    // Calculate magnitude (Pythagorean theorem)
    float mag = sqrtf(x * x + y * y);
    
    // Calculate angle (arctangent)
    float angle = RAD2DEG * atan2f(y, x);
    
    // Convert from -180° to 180° range to 0° to 360° range
    if (angle < 0.0f) {
        angle += 360.0f;
    }
    
    // If effectively zero (deadzone), mark as centred
    if (mag < 0.01f) {
        angle = -1.0f;  // Invalid angle marker
    }
    
    p.mag = mag;
    p.angle = angle;
    return p;
}
```

**Magnitude Calculation:**
- Formula: mag = √(x² + y²)
- Range: 0.0 (centred) to 1.0 (fully deflected in any direction)
- Thanks to circle mapping, this is now uniform in all directions

**Angle Calculation:**
- Formula: angle = atan2(y, x) × 180/π
- Compass convention: 0° = North, 90° = East, 180° = South, 270° = West
- Range: 0° to 360° (or -1 if centred)
- Increases clockwise (like a compass heading)

**Output:**
- `data->angle`: 0-360° or -1 if centred
- `data->magnitude`: 0.0 to 1.0

**Example Values:**

| Joystick Position | Magnitude | Angle |
|------------------|-----------|-------|
| Centre | 0.0 | -1 |
| North (up) | 1.0 | 0° |
| North-East | 1.0 | 45° |
| East (right) | 1.0 | 90° |
| South-East | 1.0 | 135° |
| South (down) | 1.0 | 180° |
| South-West | 1.0 | 225° |
| West (left) | 1.0 | 270° |
| North-West | 1.0 | 315° |

## Stage 6: Discrete Direction Output

The final stage converts the continuous angle into a discrete 8-direction output, useful for games with grid-based movement or simple controls.

```c
Direction Joystick_GetDirection(float angle)
{
    // Special case: centred
    if (angle == 0.0f || angle < 0.0f) {
        return CENTRE;
    }
    
    // Map angle ranges to 8 cardinal/intercardinal directions
    if (angle >= 337.5f || angle < 22.5f) return N;
    else if (angle >= 22.5f && angle < 67.5f) return NE;
    else if (angle >= 67.5f && angle < 112.5f) return E;
    else if (angle >= 112.5f && angle < 157.5f) return SE;
    else if (angle >= 157.5f && angle < 202.5f) return S;
    else if (angle >= 202.5f && angle < 247.5f) return SW;
    else if (angle >= 247.5f && angle < 292.5f) return W;
    else return NW;
}
```

**Direction Ranges:**

| Direction | Angle Range |
|-----------|-------------|
| CENTRE | angle = -1 |
| N (North) | 337.5° - 22.5° |
| NE (North-East) | 22.5° - 67.5° |
| E (East) | 67.5° - 112.5° |
| SE (South-East) | 112.5° - 157.5° |
| S (South) | 157.5° - 202.5° |
| SW (South-West) | 202.5° - 247.5° |
| W (West) | 247.5° - 292.5° |
| NW (North-West) | 292.5° - 337.5° |

Each direction covers a 45° arc centred on its cardinal/intercardinal heading.

**Output:**
- `data->direction`: One of 9 enum values (CENTRE, N, NE, E, SE, S, SW, W, NW)

## Choosing the Right Representation

All coordinate representations are available after calling `Joystick_Read()`. Choose based on your application needs:

### Use Raw ADC (`x_raw`, `y_raw`)
- Debugging hardware connections
- Custom processing requirements
- Checking if ADC is working correctly

### Use Processed (`x_processed`, `y_processed`)
- Custom deadzone or normalisation
- Unusual coordinate systems
- Generally avoid - better to use coord instead

### Use Cartesian (`coord.x`, `coord.y`)
- Direct position mapping (e.g., cursor control, screen position)
- 2D physics calculations
- When you need separate X and Y values

### Use Circle-Mapped Cartesian (`coord_mapped.x`, `coord_mapped.y`)
- Same as above, but want uniform feel in all directions
- Smooth analogue control with equal sensitivity

### Use Polar (`magnitude`, `angle`)
- Speed and direction control (e.g., character movement speed)
- Rotation control (e.g., turret aiming)
- When thinking in terms of "how much" and "which way"

### Use Direction (`direction` enum)
- Simple 8-way movement (retro games, grid-based games)
- Menu navigation
- When you only need discrete output

### Use UserInput Struct
- Game-style controls where you want both discrete direction and analogue magnitude
- Best of both worlds: direction for movement, magnitude for speed

## Complete Example

```c
Joystick_Read(&joy_cfg, &joy_data);

// Example 1: Cursor control using Cartesian
int cursor_x = screen_center_x + (int)(joy_data.coord.x * screen_half_width);
int cursor_y = screen_center_y - (int)(joy_data.coord.y * screen_half_height);

// Example 2: Character movement using polar
float speed = joy_data.magnitude * MAX_SPEED;
float heading = joy_data.angle;
character_x += speed * sin(heading * DEG2RAD);
character_y += speed * cos(heading * DEG2RAD);

// Example 3: Simple 8-way movement using direction
switch (joy_data.direction) {
    case N:  player_y--; break;
    case S:  player_y++; break;
    case E:  player_x++; break;
    case W:  player_x--; break;
    case NE: player_x++; player_y--; break;
    // ... etc
}

// Example 4: Menu navigation with magnitude threshold
UserInput input = Joystick_GetInput(&joy_data);
if (input.magnitude > 0.7f) {  // Only respond to strong movements
    if (input.direction == N) menu_up();
    if (input.direction == S) menu_down();
}
```

## Performance Notes

`Joystick_Read()` performs all transformations in a single call (~200μs total):
- 2× ADC conversions (~100μs each)
- Integer arithmetic (centring, deadzone)
- Floating-point normalisation
- Circle mapping (2× sqrt operations)
- Polar conversion (1× sqrt, 1× atan2)
- Direction calculation (angle comparison)

This is fast enough for typical control loops running at 100+ Hz. If you need higher performance, you can skip circle mapping and polar conversion by directly using `coord.x` and `coord.y`.
