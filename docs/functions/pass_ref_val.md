---
title: Passing values to functions
parent: Functions
nav_order: 3
layout: default
---
# Passing values to functions

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

When we pass a variable to a function, the function receives a copy of its value.

This is known as **pass by value**.

For example:

```c
#include <stdio.h>

void increment(int number);

int main(void)
{
    int value = 99;

    printf("Before: %d\n", value);

    increment(value);

    printf("After: %d\n", value);

    return 0;
}

void increment(int number)
{
    number++;

    printf("Inside increment: %d\n", number);
}
```

The output is:

```text
Before: 99
Inside increment: 100
After: 99
```

The parameter `number` receives a copy of `value`.

The function changes its local copy:

```c
number++;
```

The original variable inside `main()` is not changed.

In this example, `main()` is the caller. When we refer to the caller's variable, we mean `value`, which belongs to `main()`.

## Returning the changed value

If a function calculates a new value, the simplest approach is often to return it:

```c
#include <stdio.h>

int increment(int number);

int main(void)
{
    int value = 99;

    value = increment(value);

    printf("Value: %d\n", value);

    return 0;
}

int increment(int number)
{
    number++;

    return number;
}
```

This time, `increment()` returns `100` and `main()` assigns the returned value back to `value`:

```c
value = increment(value);
```

Returning the result is normally the clearest approach when a function produces one new value.

## When a function needs to change the original variable

Sometimes we want a function to change the variable belonging to its caller.

To do this, we pass the variable's address:

```c
increment(&value);
```

The `&` operator gives the address of `value`.

The function receives this address using a pointer parameter:

```c
void increment(int *number);
```

Inside the function, the `*` operator accesses the value stored at that address:

```c
(*number)++;
```

Here is the complete example:

```c
#include <stdio.h>

void increment(int *number);

int main(void)
{
    int value = 99;

    printf("Before: %d\n", value);

    increment(&value);

    printf("After: %d\n", value);

    return 0;
}

void increment(int *number)
{
    (*number)++;
}
```

The output is now:

```text
Before: 99
After: 100
```

The function receives the address of `value`, so it can access and change the original variable inside `main()`.

## What the pointer syntax means

There are two new operators in the previous example.

The address operator `&` gives the address of a variable:

```c
&value
```

The dereference operator `*` accesses the value stored at an address:

```c
*number
```

In the function parameter:

```c
int *number
```

`number` is a pointer to an `int`.

In the function body:

```c
*number
```

means the integer stored at the address held by `number`.

The parentheses in:

```c
(*number)++;
```

are important. We want to increment the value being pointed to, not the pointer itself.

Pointers are covered properly in the next section. For now, the main pattern is:

```c
void update_value(int *value);
```

Call it using the address of a variable:

```c
update_value(&number);
```

Access the caller's variable inside the function using:

```c
*value
```

## Is this pass by reference?

This approach is often described informally as **pass by reference** because the function can change a variable belonging to its caller.

However, C still passes the argument by value.

In this call:

```c
increment(&value);
```

the address of `value` is calculated. The function receives a copy of that address.

The copied address still refers to the original variable, so the function can change the value stored there.

The precise description is therefore:

> C passes a pointer by value, and the pointer allows the function to access the caller's variable.

You will still hear the phrase “pass by reference”, but it is useful to understand what C is actually doing.

## Comparing the two approaches

Pass the ordinary value when the function only needs to read or calculate with it:

```c
int square(int value)
{
    return value * value;
}
```

Call it using:

```c
int result = square(number);
```

Pass the address when the function needs to modify the caller's variable:

```c
void reset(int *value)
{
    *value = 0;
}
```

Call it using:

```c
reset(&number);
```

If the function only produces one result, returning that result is often clearer:

```c
number = reset_value(number);
```

Using a pointer becomes more useful when:

- the function must update an existing variable
- the function needs to produce more than one result
- copying the complete value would be unnecessary or expensive
- an existing library function expects an address

We will see these cases in more detail on the Pointers pages.

## Producing more than one result

A C function can directly return only one value:

```c
return result;
```

Pointer parameters allow a function to write several results into variables belonging to its caller.

For example:

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

This prints:

```text
Quotient: 3
Remainder: 2
```

The first two parameters are ordinary input values:

```c
int numerator
int denominator
```

The final two parameters contain addresses where the function should store its results:

```c
int *quotient
int *remainder
```

The caller passes the addresses using `&`:

```c
&quotient
&remainder
```

The function writes to those variables using `*`:

```c
*quotient = numerator / denominator;
*remainder = numerator % denominator;
```

We could also group several related results into a struct and return the struct. That approach is covered later when we combine structs with functions and pointers.

## Be careful with pointers

A pointer must refer to a valid variable before it is dereferenced.

This is valid:

```c
int value = 10;

increment(&value);
```

The pointer received by `increment()` refers to the variable `value`.

A function should not dereference an invalid address. In later examples, we will see how `NULL` is used to represent a pointer that does not currently refer to an object.

For now, only pass addresses of variables that exist:

```c
increment(&value);
```

The Pointers section explains addresses, pointer variables, dereferencing and `NULL` in more detail.