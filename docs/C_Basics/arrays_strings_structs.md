---
title: Arrays, Strings and Structs
nav_order: 4
layout: default
---

# Arrays, strings and structs

Because one variable was never going to be enough.

This section is about grouping data. An array holds many values of the same type, a string is an array of characters, and a struct bundles different values that belong together. Enums give names to a set of options, and `typedef` gives a type a shorter name. Here they all are in one small program:

```c
#include <stdio.h>

typedef enum {
    SENSOR_OK,
    SENSOR_FAULT
} sensor_status_t;

typedef struct {
    char name[16];
    int readings[3];
    sensor_status_t status;
} sensor_t;

int main(void)
{
    sensor_t sensor = { .name = "Thermistor", .readings = { 21, 22, 24 }, .status = SENSOR_OK };

    printf("%s readings:", sensor.name);
    for (int i = 0; i < 3; i++) {
        printf(" %d", sensor.readings[i]);
    }
    printf(" (%s)\n", sensor.status == SENSOR_OK ? "OK" : "FAULT");
    return 0;
}
```

This prints:

```text
Thermistor readings: 21 22 24 (OK)
```

These pages go with Units 1.2 and 1.3, and build on the loops and types in [C Basics]({% link docs/C_Basics/c_basics.md %}). Enums and typedef are the basis of the state machines in Unit 2, and [Pointers]({% link docs/pointers/pointers.md %}) shows how to pass arrays and structs to functions.
