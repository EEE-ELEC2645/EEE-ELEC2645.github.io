---
title: Testing
parent: Debugging
nav_order: 1
layout: default
---

# Introduction to Testing

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

## Introduction

Testing code is an extremely important part of the software development cycle. A top-of-the-range car, jam-packed with electronics and sensors, may have upwards of **100 million lines of code**. The fact that a *bug* in the code may cause a [fatal accident](https://en.wikipedia.org/wiki/2009%E2%80%932011_Toyota_vehicle_recalls) means that every single block of code will be tested. Indeed, software engineers often write tests **before** they write the actual code. This is known as **Test-Driven Development (TDD)**.

For example, suppose an engineer is asked to write a function to calculate the area of a circle. The function will have a single argument (the radius) and return the area. Before writing the function, they write a test that ensures the function returns the correct area for a given radius. For instance, a circle of radius `1.0 m` has an area of `3.1415926536 m²`. So they write a test that passes `1.0` to the function and checks that the return value is `3.1415926536`. Several test cases are usually included to cover edge cases such as negative numbers, very large numbers (overflow), and zero.

The tests that check individual blocks (or units) of code are called **unit tests**. Testing each function as you go saves time in the long run. For example, engineers working on the car will not write 100 million lines of code and then test everything at the end—it would be impossible to track down errors!

***

## Example

This example demonstrates writing a test for a function that calculates the sum of two integers. Although simple, it highlights the process.

Here is our simple function we want to test:

```c
int sum(int a, int b) {
    return a + b;
}
```

### Unit Test for our example Function

We can create a new function to test our `sum()` function which compares the output of `sum()` to an expected value.

```c
#include <stdio.h>
#include <stdbool.h>

// Function to test sum
bool test_sum(int a, int b, int expected) {
    printf("sum(%d,%d) : ", a, b);
    int val = sum(a, b);  // calculate value and compare to expected
    if (val == expected) {
        printf("passed\n");
        return true;
    } else {
        printf("FAILED! %d (expecting %d).\n", val, expected);
        return false;
    }
}
```

Here we pass the two arguments `a` and `b` along with the expected answer. We call `sum(a, b)` and compare the return value to the expected answer. The function returns `true` or `false` depending on whether the test passed. Debug messages are printed to the console for visibility.

***

### Function to Run Multiple Tests

Now we have a function to compare the output of `sum()`, we can create another function to run multiple tests for all different cases:

```c
int run_sum_tests() {
    printf("\nTesting sum()...\n\n");
    int passed = 0; // counter for tests passed

    // Run various tests
    if (test_sum(0, 0, 0)) passed++;
    if (test_sum(-1, 1, 0)) passed++;
    if (test_sum(2, 3, 5)) passed++;
    if (test_sum(826109, 78657567, 79483676)) passed++;
    if (test_sum(-99, -1, -100)) passed++;

    printf("\nsum() passed %d tests.\n", passed);
    return passed;
}
```

We create a counter to track passed tests and call the unit test multiple times with different parameters. If the returns `true`, we increment the counter. At the end, we return the number of tests passed.

***

## Results

Running `run_sum_tests()` produces:

```
    Testing sum()...

    sum(0,0) : passed
    sum(-1,1) : passed
    sum(2,3) : passed
    sum(826109,78657567) : passed
    sum(-99,-1) : passed

    sum() passed 5 tests.
```

All tests passed, so we can proceed with confidence that our incredibly simple program works correctly. Phew!

***

### What if the Function is Incorrect?

Now, imagine we made a mistake with our function. What if we accidentally subtracted the two numbers instead of adding them?

```c
int sum(int a, int b) {
    return a - b;
}
```

If we run the tests again, we get this

```
    Testing sum()...

    sum(0,0) : passed
    sum(-1,1) : FAILED! -2 (expecting 0).
    sum(2,3) : FAILED! -1 (expecting 5).
    sum(826109,78657567) : FAILED! -77831458 (expecting 79483676).
    sum(-99,-1) : FAILED! -98 (expecting -100).

    sum() passed 1 tests.
```

This clearly shows the function is incorrect, and we can fix it immediately. Note that one test passed because `0 + 0 = 0` and `0 - 0 = 0`. This highlights the importance of including diverse test cases!

***

## Choosing Test Cases

When writing tests, it is important to choose a variety of test cases that cover the full range of possible inputs: normal cases, edge cases, and error cases.

**Normal cases** are typical inputs that the function is expected to handle, such as positive integers for our `sum()` function.

**Edge cases** are scenarios that occur at the extreme ends of input ranges or under unusual conditions. They often reveal hidden bugs that normal cases do not. Examples include:

*   **Zero values**: `sum(0, 0)` or `sum(0, x)`
*   **Negative numbers**: `sum(-1, -99)`
*   **Large numbers**: Testing for integer overflow
*   **Boundary conditions**: Maximum and minimum values for data types

Failing to test edge cases can lead to unexpected behaviour in production. For example, if your function works for small numbers but fails for large ones, it could crash a system when handling real-world data.

**Error cases** involve invalid inputs that the function should handle gracefully, such as passing `NULL` pointers or out-of-range values. While our simple `sum()` function does not have such cases, more complex functions often do.

***

## Quick Example Using `assert()`

Whilst our manual testing functions are useful for learning, they can become tedious for larger projects. We can use some built in functionality to help us automate testing.

The C standard library provides `assert()` in `<assert.h>` for simple automated checks. If the condition inside `assert()` is false, the program terminates and reports the failure.

Here’s how you can use it:

```c
#include <assert.h>
#include <stdio.h>

int sum(int a, int b) {
    return a + b;
}

int main() {
    // Automated tests using assert
    assert(sum(0, 0) == 0);
    assert(sum(-1, 1) == 0);
    assert(sum(2, 3) == 5);
    assert(sum(-99, -1) == -100);

    printf("All tests passed!\n");
    return 0;
}
```

**Why use `assert()`?**

*   It’s quick and built into the standard library.
*   Great for small projects or sanity checks.
*   Fails fast if something is wrong.

***

However, for larger projects, consider using dedicated testing frameworks like **Unity**, **CUnit**, or **Check**. These frameworks provide more features like test suites, setup/teardown functions, and better reporting. These are also way beyond the scope of this introduction but worth exploring as you advance in C programming!
