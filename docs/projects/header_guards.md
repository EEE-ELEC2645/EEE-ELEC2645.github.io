---
title: Header guards
parent: Building C Projects
nav_order: 4
layout: default
---

# Header guards
<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

A header file may be included more than once through different parts of a project.

For example:

```text
main.c
├── game.h
│   └── maths_utils.h
└── scores.h
    └── maths_utils.h
```

Both `game.h` and `scores.h` include the same utility header:

```c
#include "maths_utils.h"
```

If `main.c` includes both headers:

```c
#include "game.h"
#include "scores.h"
```

the contents of `maths_utils.h` could be included twice.

This can cause errors if the header defines a type:

```c
typedef struct {
    int minimum;
    int maximum;
} limits_t;
```

The compiler may report that `limits_t` has been defined more than once.

## Adding a header guard

A header guard prevents the contents of a header from being processed more than once:

```c
// maths_utils.h

#ifndef MATHS_UTILS_H
#define MATHS_UTILS_H

typedef struct {
    int minimum;
    int maximum;
} limits_t;

int clamp(int value, int minimum, int maximum);

#endif
```

The guard has three parts:

```c
#ifndef MATHS_UTILS_H
```

This checks whether `MATHS_UTILS_H` has already been defined.

```c
#define MATHS_UTILS_H
```

If it has not been defined, this defines it and the compiler continues through the header.

```c
#endif
```

This marks the end of the guarded section.

If the header is included again, `MATHS_UTILS_H` is already defined, so everything between `#ifndef` and `#endif` is skipped.

## Naming the guard

The guard name is normally based on the filename, written in capital letters:

```text
maths_utils.h -> MATHS_UTILS_H
game.h        -> GAME_H
scores.h      -> SCORES_H
```

For example:

```c
#ifndef GAME_H
#define GAME_H

// Contents of game.h

#endif
```

The exact name is not important, but it must be unique within the project.

## Use guards in every header

Every project header should have a header guard:

```c
#ifndef VERY_COOL_MATHS_H
#define VERY_COOL_MATHS_H

int add_exciting_numbers(int first, int second);
int multiply_thrilling_numbers(int first, int second);

#endif
```

A useful pattern to remember is:

```c
#ifndef HEADER_NAME_H
#define HEADER_NAME_H

// Header contents

#endif
```

Some compilers also recognise:

```c
#pragma once
```

However, header guards are standard C and work across different compilers, so we use them in the module activities.