---
title: Pointers and functions
parent: Pointers
nav_order: 2
layout: default
---

# Pointers and functions

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

We previously saw that function parameters receive copies of their arguments:

```c
void restore_hp(int hp)
{
    hp = 50;
}
```

Calling this function does not change the caller's variable:

```c
int pikachu_hp = 35;

restore_hp(pikachu_hp);
```

The parameter `hp` is a separate local variable. Changing it does not change `pikachu_hp`.

## Passing an address

If a function needs to change a variable belonging to its caller, we can pass the variable's address:

```c
#include <stdio.h>

void restore_hp(int *hp);

int main(void)
{
    int pikachu_hp = 35;

    restore_hp(&pikachu_hp);

    printf("Pikachu HP: %d\n", pikachu_hp);

    return 0;
}

void restore_hp(int *hp)
{
    *hp = 50;
}
```

The caller passes the address of `pikachu_hp`:

```c
restore_hp(&pikachu_hp);
```

The function receives that address using a pointer parameter:

```c
void restore_hp(int *hp)
```

Dereferencing the pointer accesses the original variable:

```c
*hp = 50;
```

The pointer itself is still passed by value. The function receives a copy of the address, but that copied address refers to the original variable.

## Input and output parameters

An ordinary parameter is normally used to provide an input:

```c
int double_value(int value)
{
    return value * 2;
}
```

A pointer parameter can be used to provide an output:

```c
void double_value(int value, int *result)
{
    *result = value * 2;
}
```

The caller provides somewhere to store the result:

```c
int answer;

double_value(10, &answer);
```

After the call, `answer` contains `20`.

For one result, returning the value is normally clearer:

```c
int answer = double_value(10);
```

Pointer output parameters become more useful when a function needs to produce several results.

## Producing several results

A C function can return only one value directly. Pointer parameters allow it to update several variables belonging to the caller:

```c
#include <stdio.h>

void divide_with_remainder(
    int numerator,
    int denominator,
    int *quotient,
    int *remainder
);

int main(void)
{
    int quotient;
    int remainder;

    divide_with_remainder(
        17,
        5,
        &quotient,
        &remainder
    );

    printf("Quotient: %d\n", quotient);
    printf("Remainder: %d\n", remainder);

    return 0;
}

void divide_with_remainder(
    int numerator,
    int denominator,
    int *quotient,
    int *remainder
)
{
    *quotient = numerator / denominator;
    *remainder = numerator % denominator;
}
```

This produces:

```text
Quotient: 3
Remainder: 2
```

The first two parameters are inputs:

```c
int numerator
int denominator
```

The final two parameters are outputs:

```c
int *quotient
int *remainder
```

The caller passes the addresses where the results should be stored.

## Read-only pointer parameters

A function may need to read a value through a pointer without changing it.

Use `const` to show that the pointed-to value should not be modified:

```c
void print_hp(const int *hp)
{
    printf("HP: %d\n", *hp);
}
```

The function can read the value:

```c
printf("%d\n", *hp);
```

but it cannot change it:

```c
*hp = 50;    // Compiler error
```

The caller still passes an address:

```c
int pikachu_hp = 35;

print_hp(&pikachu_hp);
```

The declaration:

```c
const int *hp
```

can be read as:

> `hp` points to an integer that this function will not change.

Using `const` makes the intended use of the pointer clear and allows the compiler to catch accidental changes.

## Checking for `NULL`

A pointer parameter could be `NULL`:

```c
restore_hp(NULL);
```

Dereferencing it would be invalid.

A function can check the pointer before using it:

```c
void restore_hp(int *hp)
{
    if (hp == NULL) {
        return;
    }

    *hp = 50;
}
```

Whether a function needs this check depends on how it is designed.

For small internal functions, we may require the caller to always provide a valid address. For a function used more widely, checking for `NULL` can prevent a crash.

The important point is that a pointer must refer to a valid object before it is dereferenced.

## Returning a value or using a pointer

Return a value when the function produces one straightforward result:

```c
int double_value(int value)
{
    return value * 2;
}
```

Use a pointer when the function needs to:

- change an existing variable belonging to its caller
- produce several results
- work with a larger object without copying it

Arrays and structs make these uses more important. These are covered on the next pages.