---
title: Enums
parent: C Basics
nav_order: 9
layout: default
---

# Enums

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

An `enum`, short for **enumerated type**, gives names to a set of integer values.

For example, we could use integers to represent directions:

```c
int direction = 0;    // 0 means up
```

This works, but we have to remember what each number means. An enum makes the code clearer:

```c
enum direction {
    DIRECTION_UP,
    DIRECTION_DOWN,
    DIRECTION_LEFT,
    DIRECTION_RIGHT
};
```

We can then create a variable using the enum:

```c
enum direction player_direction = DIRECTION_UP;
```

It is much easier to understand `DIRECTION_UP` than an unexplained value such as `0`.

## Enum values

By default, the first item in an enum has the value 0. Each item after it increases by one:

```c
enum direction {
    DIRECTION_UP,       // 0
    DIRECTION_DOWN,     // 1
    DIRECTION_LEFT,     // 2
    DIRECTION_RIGHT     // 3
};
```

You can assign the values explicitly:

```c
enum direction {
    DIRECTION_UP = 0,
    DIRECTION_DOWN = 1,
    DIRECTION_LEFT = 2,
    DIRECTION_RIGHT = 3
};
```

Both versions create the same values.

If you assign a value to one item, the following items continue counting from that value:

```c
enum pokemon {
    POKEMON_BULBASAUR = 1,
    POKEMON_IVYSAUR,       // 2
    POKEMON_VENUSAUR,      // 3
    POKEMON_CHARMANDER,    // 4
    POKEMON_CHARMELEON,    // 5
    POKEMON_CHARIZARD      // 6
};
```

You can also give each item a particular value:

```c
enum ansi_background_colour {
    ANSI_BACKGROUND_BLACK = 40,
    ANSI_BACKGROUND_RED = 41,
    ANSI_BACKGROUND_GREEN = 42,
    ANSI_BACKGROUND_BLUE = 44,
    ANSI_BACKGROUND_WHITE = 47
};
```

This is useful when the numbers are defined by hardware, a communication protocol or another standard.

## Using an enum variable

Once an enum has been declared, we can use it as a type:

```c
#include <stdio.h>

enum direction {
    DIRECTION_UP,
    DIRECTION_DOWN,
    DIRECTION_LEFT,
    DIRECTION_RIGHT
};

int main(void)
{
    enum direction player_direction = DIRECTION_UP;

    player_direction = DIRECTION_LEFT;

    if (player_direction == DIRECTION_LEFT) {
        printf("Moving left\n");
    }

    return 0;
}
```

The variable still stores an integer, but the named values make its intended purpose clearer.

## Using enums with `switch`

Enums are particularly useful with `switch` statements:

```c
#include <stdio.h>

enum direction {
    DIRECTION_UP,
    DIRECTION_DOWN,
    DIRECTION_LEFT,
    DIRECTION_RIGHT
};

int main(void)
{
    enum direction player_direction = DIRECTION_RIGHT;

    switch (player_direction) {
        case DIRECTION_UP:
            printf("Moving up\n");
            break;

        case DIRECTION_DOWN:
            printf("Moving down\n");
            break;

        case DIRECTION_LEFT:
            printf("Moving left\n");
            break;

        case DIRECTION_RIGHT:
            printf("Moving right\n");
            break;

        default:
            printf("Unknown direction\n");
            break;
    }

    return 0;
}
```

This is easier to read than using unexplained numbers in each `case`.

We will also use this pattern later when writing finite-state machines. Each enum value can represent one possible state:

```c
enum system_state {
    STATE_IDLE,
    STATE_RUNNING,
    STATE_ERROR
};
```

## Naming enum values

Enum values share their names with other identifiers in the same scope. It is therefore useful to give related values a common prefix:

```c
enum direction {
    DIRECTION_UP,
    DIRECTION_DOWN,
    DIRECTION_LEFT,
    DIRECTION_RIGHT
};
```

Rather than:

```c
enum direction {
    UP,
    DOWN,
    LEFT,
    RIGHT
};
```

The prefix makes it clear which enum the value belongs to and reduces the chance of a name conflicting with something else.

For example:

```c
enum fruit {
    FRUIT_APPLE,
    FRUIT_BANANA,
    FRUIT_PEAR
};

enum vegetable {
    VEGETABLE_POTATO,
    VEGETABLE_CARROT,
    VEGETABLE_ONION
};
```

## Enums are still integers

In C, enum values are represented using integers. This means the compiler does not completely prevent you from assigning an unrelated number:

```c
enum direction player_direction = DIRECTION_UP;

player_direction = 20;
```

Depending on the compiler options, this may compile without an error even though `20` is not one of the named directions.

For this reason, enums do not guarantee that a variable contains only one of the listed values. They mainly make the intended values easier to understand and help the compiler produce useful warnings in some situations.

A `default` case can handle an unexpected value:

```c
switch (player_direction) {
    case DIRECTION_UP:
        printf("Moving up\n");
        break;

    case DIRECTION_DOWN:
        printf("Moving down\n");
        break;

    case DIRECTION_LEFT:
        printf("Moving left\n");
        break;

    case DIRECTION_RIGHT:
        printf("Moving right\n");
        break;

    default:
        printf("Invalid direction\n");
        break;
}
```

## Comparing different enum types

Different enum types may use the same underlying integer values:

```c
enum fruit {
    FRUIT_APPLE,       // 0
    FRUIT_BANANA       // 1
};

enum vegetable {
    VEGETABLE_POTATO,  // 0
    VEGETABLE_CARROT   // 1
};
```

This makes the following comparison possible in C:

```c
enum fruit fruit = FRUIT_APPLE;
enum vegetable vegetable = VEGETABLE_POTATO;

if (fruit == vegetable) {
    printf("The integer values are equal\n");
}
```

Both values happen to be zero, so the comparison may be true even though an apple is obviously not a potato!

There is normally no reason to compare values from unrelated enums. Using clear variable names and prefixes makes this sort of mistake easier to spot.

## When to use an enum

Use an enum when a variable has a small, fixed set of meaningful options, such as:

```c
enum direction {
    DIRECTION_UP,
    DIRECTION_DOWN,
    DIRECTION_LEFT,
    DIRECTION_RIGHT
};
```

```c
enum system_state {
    STATE_IDLE,
    STATE_RUNNING,
    STATE_ERROR
};
```

```c
enum menu_option {
    MENU_START,
    MENU_SETTINGS,
    MENU_EXIT
};
```

An enum is usually clearer than scattering unexplained numbers throughout the program.