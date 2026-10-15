---
title: Arrays with pointers
parent: Pointers
nav_order: 3
layout: default
---
# Arrays and pointers

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

Arrays and pointers are closely related in C.

Consider this array:

```c
int values[] = {10, 20, 30, 40, 50};
```

In most expressions, the array name gives the address of its first element:

```c
values
```

This is the same address as:

```c
&values[0]
```

We can therefore use the array name to initialise a pointer:

```c
int *value_pointer = values;
```

The pointer now targets the first element of the array.

## Accessing array elements

Dereferencing the pointer accesses the first element:

```c
*value_pointer
```

This is equivalent to:

```c
values[0]
```

Adding one to the pointer moves it to the next element:

```c
*(value_pointer + 1)
```

This accesses:

```c
values[1]
```

In general:

```c
values[index]
```

is equivalent to:

```c
*(values + index)
```

Square brackets are normally easier to read and what we will focus on in the course, but the pointer form helps explain how array indexing actually works.

## Passing an array to a function

When an array is passed to a function, the function receives a pointer to its first element.

For example:

```c
int sum_array(const int values[], size_t length)
```

The parameter:

```c
const int values[]
```

can also be written as:

```c
const int *values
```

The function does not receive the complete array as a copy.

## Passing the length

A pointer tells the function where the array begins, but not how many elements it contains. We must pass the length separately:

```c
#include <stdio.h>

int sum_array(const int values[], size_t length);

int main(void)
{
    int values[] = {10, 20, 30, 40, 50};
    size_t length = sizeof(values) / sizeof(values[0]);

    int total = sum_array(values, length);

    printf("Total: %d\n", total);

    return 0;
}

int sum_array(const int values[], size_t length)
{
    int total = 0;

    for (size_t i = 0; i < length; i++) {
        total = total + values[i];
    }

    return total;
}
```

The caller calculates and passes the length:

```c
sum_array(values, length);
```

Inside `sum_array()`, `values` is a pointer. This means that:

```c
sizeof(values)
```

would give the size of the pointer, *not* the size of the original array!

## Modifying an array

A function can modify the original array:

```c
void double_array(int values[], size_t length)
{
    for (size_t i = 0; i < length; i++) {
        values[i] = values[i] * 2;
    }
}
```

Calling:

```c
double_array(values, length);
```

changes the elements in the caller's array.

If the function should only read the array, use `const`:

```c
int sum_array(const int values[], size_t length)
```

This allows the function to read the elements but prevents it from changing them. Not essential, but it is good practice. 

The main points to remember are:

- an array name normally gives the address of its first element
- array indexing is closely related to pointer arithmetic
- a function receiving an array does not know its length
- pass the array length as a separate parameter
- use `const` when the function should not modify the array