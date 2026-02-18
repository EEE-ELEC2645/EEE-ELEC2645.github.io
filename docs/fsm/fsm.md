---
title: Finite State Machines (FSM)
nav_order: 15
layout: default
---

# Finite State Machines (FSM)

For when your code needs to know *where it's been* and *where it's going*!

```c
#include <stdio.h>

// Simple traffic light FSM
typedef enum {
    RED,
    GREEN,
    YELLOW
} TrafficLightState;

int main(void) {
    TrafficLightState current_light = RED;
    
    // Process button press - transition to next state
    if (current_light == RED) {
        current_light = GREEN;
        printf("🚦 Green light! Go go go!\n");
    }
    else if (current_light == GREEN) {
        current_light = YELLOW;
        printf("🚦 Yellow light! Slowing down...\n");
    }
    else if (current_light == YELLOW) {
        current_light = RED;
        printf("🚦 Red light! Stop!\n");
    }
    
    return 0;
}
```

FSMs are your best friend when you have clear states and rules for moving between them! :D

