---
title: Typedef
parent: C Basics
nav_order: 12
layout: default
---

# `typedef`

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

The `typedef` keyword creates a shorter name for an existing type.

The basic syntax is:

```c
typedef existing_type new_name;
```

For example:

```c
typedef unsigned int uint;
```

We can then write:

```c
uint count = 10;
```

instead of:

```c
unsigned int count = 10;
```

This does not create a completely new type. `uint` is simply another name for `unsigned int`.

You will usually see `typedef` used with structs and enums, where it saves us from repeatedly writing `struct` or `enum`.

## Using `typedef` with a struct

Without `typedef`, we include the `struct` keyword whenever we create a variable:

```c
struct point {
    int x;
    int y;
};

struct point position = {
    .x = 10,
    .y = 20
};
```

We can define a shorter type name using `typedef`:

```c
typedef struct {
    int x;
    int y;
} point_t;
```

We can then create variables using `point_t`:

```c
point_t position = {
    .x = 10,
    .y = 20
};
```

This is a common pattern in C:

```c
typedef struct {
    int sensor_id;
    float value;
} sensor_reading_t;
```

The name after the closing brace is the new type name:

```c
sensor_reading_t reading;
```

## Using `typedef` with an enum

Without `typedef`, an enum variable is declared like this:

```c
enum direction {
    DIRECTION_UP,
    DIRECTION_DOWN,
    DIRECTION_LEFT,
    DIRECTION_RIGHT
};

enum direction player_direction = DIRECTION_UP;
```

Using `typedef`, we can create a shorter type name:

```c
typedef enum {
    DIRECTION_UP,
    DIRECTION_DOWN,
    DIRECTION_LEFT,
    DIRECTION_RIGHT
} direction_t;
```

We can then write:

```c
direction_t player_direction = DIRECTION_UP;
```

This style is commonly used for program states:

```c
typedef enum {
    STATE_IDLE,
    STATE_RUNNING,
    STATE_ERROR
} state_t;
```

## A complete example

This example combines a typedef struct and a typedef enum:

```c
#include <stdio.h>

typedef enum {
    DIRECTION_UP,
    DIRECTION_DOWN,
    DIRECTION_LEFT,
    DIRECTION_RIGHT
} direction_t;

typedef struct {
    int x;
    int y;
    direction_t direction;
} player_t;

int main(void)
{
    player_t player = {
        .x = 10,
        .y = 20,
        .direction = DIRECTION_RIGHT
    };

    printf("Player position: (%d, %d)\n", player.x, player.y);

    return 0;
}
```

The type names are:

```c
direction_t
player_t
```

The enum values are:

```c
DIRECTION_UP
DIRECTION_DOWN
DIRECTION_LEFT
DIRECTION_RIGHT
```

## The `_t` suffix

A common convention is to end typedef names with `_t`:

```c
point_t
direction_t
player_t
```

This makes it easier to recognise that the name represents a type.

We use this convention in some of the module libraries, although C does not require it.

## Do not overuse `typedef`

A typedef should make the code easier to read.

This is useful:

```c
typedef struct {
    int x;
    int y;
} point_t;
```

This is probably not:

```c
typedef int number;
```

The name `number` does not tell us anything more than `int`.

For now, the main thing to remember is that `typedef` gives an existing type another name. You will most often use it to create shorter names for structs and enums.