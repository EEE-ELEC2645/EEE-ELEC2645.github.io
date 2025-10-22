---
title: Booleans
parent: C Basics
nav_order: 11
layout: default
---

# Booleans in C

C doesn't have a built-in `bool` type in its original standard (C89/C90), but since C99, you can include `<stdbool.h>` for a more readable `bool` type.

```c
#include <stdbool.h>

bool is_ready = true;
bool has_error = false;
```

This makes your code more expressive and readable - especially useful in embedded systems where clarity matters.

---

## How Booleans Work Behind the Scenes

In C:

- `false` is defined as `0`
- `true` is defined as `1`
- Any **non-zero** value is considered **true** in conditional expressions

```c
#include <stdio.h>  // For printf() etc. 
#include <stdbool.h>  // For boolean data type (bool: true, false) added in C99

int x = 5;
int y = 0;

bool a = true;  
bool b = false; 


// using ints for conditionals

if (x) {
    // This runs because x is non-zero (true)
}

if (y) {
    // This doesn't because y is zero (false)
}

// using bools

    if (a == true) {
        // this runs as a is true
    }
 
    // You can print bools like integers (true = 1, false = 0)
    
    printf("A is : %d\n", a);  // prints A is 1
    printf("B is  : %d\n", b); // prints B is 0
    
    // you can assign bools like ints
    a = 0; // true
    b = 1; // false 
    b = 2; // will actually be 1/true as the max value a bool can be is 1 
 
    printf("A is : %d\n", a); // prints A is 1
    printf("B is  : %d\n", b); // prints B is 1
    
    if (b == true) {
       // runs as b is now true
    }
```

---

## Example: Simple Status Check

Let's say you're checking if a sensor is active and if data is valid:

```c
#include <stdbool.h>

bool sensor_active = true;
bool data_valid = false;

if (sensor_active && data_valid) {
    // Both conditions must be true
    // Process the data
} else {
    // Either sensor is inactive or data is invalid
}
```

---

## Embedded Style Tip

Use booleans to make your intent clear, but **avoid relying on implicit truthiness** for critical logic. For example:

```c
// Clear
if (sensor_active == true) { ... }
if (sensor_active == 1) {...}

// Less clear i.e. "implicit truthiness" did you mean if its 1?  or just above 0? 
if (sensor_active) { ... }
```

In embedded systems, often clarity beats brevity - especially when debugging functions using hardware

---

## Mini Utility: Toggle a Boolean

```c
sensor_active = !sensor_active;
```

This flips the value - useful for toggling states like LEDs, modes, or flags.

---

## Example Checking Sensor Logic

If you had a sensor that your microcontroller was in contact with, you might have a logic like this to check connections and if there is data ready to receive.

```c
#include <stdio.h>
#include <stdbool.h>

// Simulated sensor state
bool sensor_connected = true;
bool sensor_data_ready = false;

int main(void) {
    // Step 1: Check if sensor is connected
    if (!sensor_connected) {
        printf("Sensor not connected. Please check wiring.\n");
        return 1; // Early exit
    }

    // Step 2: Simulate data becoming ready
    sensor_data_ready = true; // In real code, this might come from a hardware flag

    // Step 3: Process data if ready
    if (sensor_data_ready) {
        printf("Sensor data is ready. Processing...\n");
        // Simulate processing
    } else {
        printf("Waiting for sensor data...\n");
    }

    return 0;
}
```
