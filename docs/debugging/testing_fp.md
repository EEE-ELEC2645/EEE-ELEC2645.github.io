---
title: Floating-Point Tests
parent: Debugging
nav_order: 2
layout: default
---

# Testing Floating-Point Functions in C

<details markdown="block">
  <summary>
    Table of Contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

## Introduction

In the `sum()` example, we used `==` to compare integers. That works fine for integers and booleans, but **not for floating-point numbers** (`float` and `double`). Why? Because floating-point numbers are stored in binary and often cannot represent decimal values exactly.

For example, two variables that should both hold `1.0` might actually be:

```
1.000000000003232
1.000000000012565
```

Comparing these with `==` will fail, even though they are “close enough.” So we need a different approach.

---

## Comparing Floating-Point Numbers

Instead of checking for exact equality, we check if the numbers are **almost equal** within a small tolerance:

```c
#include <math.h>
#include <stdbool.h>

// Returns true if a and b differ by less than 1e-6
bool almost_equal(double a, double b) {
    return fabs(a - b) <= 1e-6;
}
```

This uses `fabs()` from `<math.h>` to get the absolute difference and compares it to a threshold (`1e-6`).

***

## Example: Testing a Square Function

Suppose we have a function `sqr()` that returns the square of a number:

```c
double sqr(double a) {
    return a * a;
}
```

### Unit Test for `sqr()`

We write a test function that uses `almost_equal()`:

```c
#include <stdio.h>

bool test_sqr(double input, double expected) {
    printf("sqr(%.6f): ", input);
    double val = sqr(input);
    if (almost_equal(val, expected)) {
        printf("passed\n");
        return true;
    } else {
        printf("FAILED! %.6f (expected %.6f)\n", val, expected);
        return false;
    }
}
```

***

### Running Multiple Tests

```c
int run_sqr_tests(void) {
    printf("\nTesting sqr()...\n\n");
    int passed = 0;

    if (test_sqr(-1.123456, 1.262155)) passed++;
    if (test_sqr(3.0, 9.0)) passed++;
    if (test_sqr(7.678, 58.951684)) passed++;
    if (test_sqr(0.00001, 0.0000000001)) passed++;

    printf("\nsqr() passed %d tests.\n", passed);
    return passed;
}
```

***

## Results

Running `run_sqr_tests()` produces:

    Testing sqr()...

    sqr(-1.123456): passed
    sqr(3.000000): passed
    sqr(7.678000): passed
    sqr(0.000010): passed

    sqr() passed 4 tests.

***

## Why Use a Tolerance?

Floating-point numbers can vary by tiny amounts due to rounding. Using a tolerance like `1e-6` means we accept small differences, which is usually fine for most engineering calculations.

***

## Quick Example Using `assert()`

For automated checks, we can use `assert()`:

```c
#include <assert.h>
#include <math.h>

double sqr(double a) { return a * a; }
bool almost_equal(double a, double b) { return fabs(a - b) <= 1e-6; }

int main(void) {
    assert(almost_equal(sqr(-1.123456), 1.262155));
    assert(almost_equal(sqr(3.0), 9.0));
    assert(almost_equal(sqr(7.678), 58.951684));
    assert(almost_equal(sqr(0.00001), 0.0000000001));
    return 0;
}
```

If any test fails, the program stops immediately.

***

### Key Points

*   Avoid `==` for floating-point comparisons.
*   Use a small tolerance (e.g., `1e-6`) for “almost equal.”
*   Test normal, negative, and very small values.
*   `assert()` is great for quick automated checks.

***
