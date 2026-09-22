---
title: Structs
parent: C Basics
nav_order: 10
layout: default
---

# Structs

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

A `struct` groups several related variables into a single type.

For example, a two-dimensional point has an `x` coordinate and a `y` coordinate. We could store these as separate variables:

```c
int point_x = 10;
int point_y = 20;
```

However, these values belong together. A struct lets us group them:

```c
struct point {
    int x;
    int y;
};
```

This defines a new structure called `struct point`. It contains two members, `x` and `y`.

## Creating a struct variable

After defining the structure, we can create variables of that type:

```c
struct point player_position;
```

We access each member using the dot operator `.`:

```c
player_position.x = 10;
player_position.y = 20;
```

We can then use the members like normal variables:

```c
#include <stdio.h>

struct point {
    int x;
    int y;
};

int main(void)
{
    struct point player_position;

    player_position.x = 10;
    player_position.y = 20;

    printf(
        "Position: (%d, %d)\n",
        player_position.x,
        player_position.y
    );

    return 0;
}
```

This prints:

```text
Position: (10, 20)
```

## Initialising a struct

A struct can be initialised when it is created:

```c
struct point origin = {0, 0};
```

The values are assigned in the same order as the members in the definition:

```c
struct point {
    int x;
    int y;
};
```

Therefore:

```c
struct point position = {10, 20};
```

sets `x` to 10 and `y` to 20.

This works, but it is easy to put the values in the wrong order, particularly when a struct contains lots of members.

## Designated initialisers

A designated initialiser identifies each member by name:

```c
struct point destination = {
    .x = 45,
    .y = 76
};
```

This is normally clearer because we can see which value belongs to each member.

The members do not have to appear in the same order as the structure definition:

```c
struct point destination = {
    .y = 76,
    .x = 45
};
```

Both versions produce the same result.

You can also provide values for only some of the members:

```c
struct point position = {
    .x = 10
};
```

Here, `x` is set to 10 and `y` is set to zero.

## Different types in one struct

The members of a struct do not all need to have the same type:

```c
#include <stdbool.h>

struct sensor_reading {
    int sensor_id;
    float value;
    bool valid;
};
```

We can create and initialise a sensor reading:

```c
struct sensor_reading reading = {
    .sensor_id = 3,
    .value = 24.5f,
    .valid = true
};
```

Remember that `<stdbool.h>` is needed to use `bool`, `true` and `false`.

We can access each member using `.`:

```c
printf("Sensor: %d\n", reading.sensor_id);
printf("Value: %.1f\n", reading.value);

if (reading.valid) {
    printf("The reading is valid\n");
}
```

## Changing members

Individual members can be changed after the struct has been created:

```c
struct point position = {
    .x = 10,
    .y = 20
};

position.x = 15;
position.y += 5;
```

The final position is:

```text
(15, 25)
```

## Copying a struct

Unlike arrays, structs can be copied using `=`:

```c
struct point first = {
    .x = 10,
    .y = 20
};

struct point second = first;
```

This copies the value of every member from `first` into `second`.

The two variables are still separate. Changing one does not change the other:

```c
second.x = 100;

printf("first.x = %d\n", first.x);    // 10
printf("second.x = %d\n", second.x);  // 100
```

## Assigning a complete new value

We can assign values to individual members:

```c
position.x = 12;
position.y = 15;
```

We can also replace the complete struct using a **compound literal**:

```c
position = (struct point) {
    .x = 12,
    .y = 15
};
```

The `(struct point)` part tells C what type of value is being created.

This is useful when several members should be updated together.

## Structs and functions

A struct can be passed to a function in the same way as other variables:

```c
#include <stdio.h>

struct point {
    int x;
    int y;
};

void print_point(struct point position)
{
    printf("(%d, %d)\n", position.x, position.y);
}

int main(void)
{
    struct point player_position = {
        .x = 10,
        .y = 20
    };

    print_point(player_position);

    return 0;
}
```

In this example, the complete struct is passed to `print_point()`.

The function receives a copy, so changing it inside the function would not change the original variable.

## Returning a struct from a function

A function can also return a struct:

```c
struct point make_point(int x, int y)
{
    struct point new_point = {
        .x = x,
        .y = y
    };

    return new_point;
}
```

We can use it like this:

```c
struct point destination = make_point(45, 76);
```

This is useful when a function needs to calculate and return several related values.

A shorter version is:

```c
struct point make_point(int x, int y)
{
    return (struct point) {
        .x = x,
        .y = y
    };
}
```

Both versions do the same thing.

## Arrays of structs

We can create an array containing several structs:

```c
struct point path[] = {
    { .x = 0,  .y = 0  },
    { .x = 10, .y = 5  },
    { .x = 20, .y = 15 }
};
```

We use an array index to choose a struct, followed by `.` to choose one of its members:

```c
printf("%d\n", path[1].x);
```

This prints:

```text
10
```

We can process the complete array using a `for` loop:

```c
#include <stdio.h>

struct point {
    int x;
    int y;
};

int main(void)
{
    struct point path[] = {
        { .x = 0,  .y = 0  },
        { .x = 10, .y = 5  },
        { .x = 20, .y = 15 }
    };

    size_t length = sizeof(path) / sizeof(path[0]);

    for (size_t i = 0; i < length; i++) {
        printf(
            "path[%zu] = (%d, %d)\n",
            i,
            path[i].x,
            path[i].y
        );
    }

    return 0;
}
```

## Structs containing arrays

A struct member can also be an array:

```c
struct student {
    char name[30];
    int marks[4];
};
```

For example:

```c
struct student student = {
    .name = "Ash Ketchum",
    .marks = {65, 72, 58, 81}
};
```

Individual values are accessed in the usual way:

```c
printf("Name: %s\n", student.name);
printf("First mark: %d\n", student.marks[0]);
```

## Comparing structs

C does not allow complete structs to be compared using `==`:

```c
struct point first = { .x = 10, .y = 20 };
struct point second = { .x = 10, .y = 20 };

if (first == second) {
    // This is not allowed
}
```

Instead, compare the relevant members:

```c
if (first.x == second.x && first.y == second.y) {
    printf("The points are equal\n");
}
```

A function can make this easier:

```c
#include <stdbool.h>

bool points_are_equal(struct point first, struct point second)
{
    return first.x == second.x && first.y == second.y;
}
```

## The `struct` keyword

In C, the full type name includes the `struct` keyword:

```c
struct point position;
```

Writing only:

```c
point position;
```

does not work with the definition used on this page.

Later, we will see how `typedef` can create a shorter type name:

```c
point_t position;
```

For now, use `struct point`.