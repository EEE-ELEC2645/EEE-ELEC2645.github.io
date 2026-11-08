---
title: Floating-Point Tests
parent: Debugging
nav_order: 2
layout: default
---

# Testing floating-point values

<details markdown="block">
  <summary>
    Table of Contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

Floating-point numbers cannot always represent decimal values exactly.

For example:

```c
double value = 0.1 + 0.2;
```

Mathematically, we expect:

```text
0.3
```

However, floating-point values are stored in binary, so small rounding errors can occur.

This means that two values which should be equal may differ by a tiny amount. This is a very common source of bugs and confusion!

## Comparing floating-point values

Avoid testing floating-point values using:

```c
if (a == b)
```

For example:

```c
double result = 0.1 + 0.2;

if (result == 0.3) {
    printf("Equal\n");
}
```

This may not behave as expected.

Instead, check whether the difference between the values is small enough:

```c
#include <math.h>

if (fabs(a - b) < 1e-6) {
    printf("Close enough\n");
}
```

The value:

```c
1e-6
```

is known as a **tolerance**.

## A helper function

A helper function makes this easier:

```c
#include <math.h>
#include <stdbool.h>

bool almost_equal(double a, double b)
{
    return fabs(a - b) < 1e-6;
}
```

We can then write:

```c
if (almost_equal(result, 0.3)) {
    printf("PASS\n");
}
```

## Testing a function

Suppose we have:

```c
double calculate_average(
    double a,
    double b
)
{
    return (a + b) / 2.0;
}
```

A test might be:

```c
if (almost_equal(
        calculate_average(10.0, 20.0),
        15.0)) {
    printf("PASS\n");
} else {
    printf("FAIL\n");
}
```

This compares the values within a tolerance rather than requiring them to be exactly identical.

## Choosing a tolerance

The best tolerance depends on the application.

For the examples in this module, values such as:

```c
1e-6
```

or:

```c
1e-5
```

are usually reasonable.

Make the tolerance small enough to detect genuine mistakes, but large enough to allow for normal floating-point rounding errors.

## A useful rule

For integers:

```c
a == b
```

is usually fine.

For floating-point values:

```c
fabs(a - b) < tolerance
```

is usually a better choice.