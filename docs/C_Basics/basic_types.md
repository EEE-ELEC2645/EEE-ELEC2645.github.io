---
title: Basic Data Types
parent: C Basics
nav_order: 1
layout: default
---

# Basic data types in C

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


Every variable in C has a **type**. The type tells the compiler what kind of value the variable stores and what operations can be performed on it.

For example:

```c
int score = 42;
float temperature = 21.5f;
char grade = 'A';
```

Here:

- `score` stores an integer
- `temperature` stores a number with a fractional part
- `grade` stores one character

## Declaring a variable

A variable declaration normally contains a type, a name and an initial value:

```c
int count = 0;
```

The parts are:

```text
type      name        initial value
int       count       = 0;
```

You can declare a variable without giving it an initial value:

```c
int count;
```

However, a local variable declared this way contains an indeterminate value until something is assigned to it. Using it before setting a value produces undefined behaviour.

Where possible, initialise variables when they are declared:

```c
int count = 0;
```

## Integer types

The `int` type stores a whole number:

```c
int score = 42;
int temperature = -5;
```

Print an `int` using `%d`:

```c
#include <stdio.h>

int main(void)
{
    int score = 42;

    printf("Score: %d\n", score);

    return 0;
}
```

An `int` cannot store a fractional part:

```c
int value = 3.7;
```

The value is converted to `3`, so the fractional part is lost.

## Signed and unsigned integers

Integer types can be **signed** or **unsigned**.

A signed integer can store positive and negative values:

```c
int temperature = -10;
```

An unsigned integer stores only zero and positive values:

```c
unsigned int count = 10;
```

Print an `unsigned int` using `%u`:

```c
unsigned int count = 10;

printf("Count: %u\n", count);
```

You can make the unsigned type explicit in the value using the `u` suffix:

```c
unsigned int count = 10u;
```

Do not use an unsigned type merely because a value should not normally be negative. Unsigned arithmetic behaves differently around zero:

```c
unsigned int count = 0;

count--;

printf("%u\n", count);
```

This does not produce `-1`. The value wraps around to the largest value the type can represent.

Unsigned types are useful when we specifically need their range or behaviour, but signed integers are often simpler for ordinary counting and calculations.

## Other integer types

C provides several integer types:

```c
char
short
int
long
long long
```

Each also has signed and unsigned forms:

```c
signed int
unsigned int

signed long
unsigned long
```

The exact size of these types can vary between systems. The C standard only guarantees that:

```text
sizeof(char) <= sizeof(short) <= sizeof(int) <= sizeof(long) <= sizeof(long long)
```

In practice, `int` is usually the sensible choice for ordinary whole-number calculations.

Use `long` or `long long` when you know that the range of `int` is not sufficient:

```c
long distance = 123456L;
long long population = 8000000000LL;
```

The suffixes identify the type of the literal:

```c
123456L          // long
8000000000LL     // long long
42U              // unsigned int
42UL             // unsigned long
```

The fixed-width types from `<stdint.h>`, such as `uint8_t` and `int32_t`, are covered on the next page.

## Characters

The `char` type stores one byte. It is commonly used to store an ASCII character:

```c
char grade = 'A';
char response = 'y';
char symbol = '#';
```

A character uses single quotation marks:

```c
char letter = 'A';
```

Double quotation marks create a string instead:

```c
char word[] = "A";
```

These are not the same. The string contains both the character `'A'` and the null terminator `'\0'`.

Print a character using `%c`:

```c
char grade = 'A';

printf("Grade: %c\n", grade);
```

A `char` is also an integer type, so the underlying numeric value can be printed:

```c
char letter = 'A';

printf("Character: %c\n", letter);
printf("Numeric value: %d\n", letter);
```

On a system using ASCII, this prints:

```text
Character: A
Numeric value: 65
```

Whether plain `char` behaves as signed or unsigned is implementation-defined. If your code specifically needs a small number rather than a character, use `signed char`, `unsigned char`, or one of the fixed-width types from `<stdint.h>`.

## Floating-point types

Floating-point types store numbers with a fractional part.

The two types you will use most often are:

```c
float
double
```

For example:

```c
float temperature = 21.5f;
double pi = 3.141592653589793;
```

A floating-point literal such as `21.5` has type `double` by default. Add `f` when you want a `float` literal:

```c
float temperature = 21.5f;
```

Both `float` and `double` use `%f` with `printf`:

```c
#include <stdio.h>

int main(void)
{
    float temperature = 21.5f;
    double pi = 3.141592653589793;

    printf("Temperature: %f\n", temperature);
    printf("Pi: %f\n", pi);

    return 0;
}
```

We can control the number of digits printed after the decimal point:

```c
printf("Temperature: %.1f\n", temperature);
printf("Pi: %.3f\n", pi);
```

This prints:

```text
Temperature: 21.5
Pi: 3.142
```

Be careful with `scanf`, where the formats for `float` and `double` are different:

```c
float value_f;
double value_d;

scanf("%f", &value_f);
scanf("%lf", &value_d);
```

The more detailed problems with reading and validating input are covered on the User Input Handling page.

## Floating-point values are approximate

Many decimal values cannot be represented exactly using binary floating point, see the [Computerphile Classic](https://www.youtube.com/watch?v=PZRI1IfStY0):

```c
float value = 0.1f;
```

The value stored is a close approximation to `0.1`, rather than the exact decimal fraction.

This means direct equality checks can be unreliable after calculations:

```c
float value = 0.1f + 0.2f;

if (value == 0.3f) {
    // This may not run
}
```

For measured values, it is normally better to check whether the difference is sufficiently small:

```c
#include <math.h>

float difference = fabsf(value - 0.3f); // get the absolute value

if (difference < 0.0001f) {
    printf("The values are close enough\n");
}
```

The acceptable difference depends on the values being measured and the accuracy required.

## Integer and floating-point division

When both operands are integers, C performs integer division:

```c
int result = 10 / 3;
```

The result is `3`, not `3.333`. I guarantee this will cause a bug in your program at some point! :D

If at least one operand is floating point, the result is floating point:

```c
float result = 10.0f / 3.0f;
```

Be careful with this:

```c
float result = 10 / 3;
```

The division happens first using integers, producing `3`. That value is then converted to `3.0f`.

You could also convert one operand explicitly:

```c
float result = (float)10 / 3;
```

The `(float)` is known as a **cast**. It converts the value to `float` before the division takes place.

## Finding the size of a type

The `sizeof` operator gives the size of a type or variable in bytes:

```c
sizeof(int)
sizeof(float)
sizeof(char)
```

It returns a value of type `size_t`, which is printed using `%zu`:

```c
#include <stdio.h>

int main(void)
{
    printf("char: %zu byte(s)\n", sizeof(char));
    printf("int: %zu byte(s)\n", sizeof(int));
    printf("float: %zu byte(s)\n", sizeof(float));
    printf("double: %zu byte(s)\n", sizeof(double));

    return 0;
}
```

The size of `char` is always one byte by definition:

```c
sizeof(char) == 1
```

However, a C byte is not required to contain exactly eight bits on every possible system. For the PCs and STM32 boards used in this module, a byte contains eight bits.

The sizes of the other basic types depend on the compiler and target system. Do not write code that assumes `int` is always four bytes.

## Format specifiers

The format supplied to `printf` must match the type of the argument:

| Type | `printf` format |
|:-----|:----------------|
| `char` as a character | `%c` |
| `int` | `%d` |
| `unsigned int` | `%u` |
| `long` | `%ld` |
| `unsigned long` | `%lu` |
| `long long` | `%lld` |
| `unsigned long long` | `%llu` |
| `float` or `double` | `%f` |
| `long double` | `%Lf` |
| `size_t` | `%zu` |

For example:

```c
int score = 42;
unsigned int count = 10u;
float temperature = 21.5f;

printf("Score: %d\n", score);
printf("Count: %u\n", count);
printf("Temperature: %.1f\n", temperature);
```

Using the wrong format specifier can produce incorrect output or undefined behaviour. Compiler warnings will often catch a mismatch:

```bash
-Wall -Wextra -Wpedantic
```

Pay attention to these warnings rather than assuming that the program is correct because it compiled.

## Choosing a type

For the moment, these are useful starting points:

- use `int` for ordinary whole-number calculations
- use `float` when you need a fractional value and its precision is sufficient
- use `double` when you need more precision
- use `char` for individual characters
- use `bool` for true-or-false values
- use `size_t` for sizes and array indices based on `sizeof`
- use the `<stdint.h>` types when the exact width matters

There is no need to choose the smallest possible type for every local variable. The type should first make the meaning of the variable clear and provide the range required by the calculation.
