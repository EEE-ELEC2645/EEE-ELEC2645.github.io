---
title: Arrays
parent: C Basics
nav_order: 6
layout: default
---

# Arrays in C

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# Arrays in C

An array stores several values of the same type under one name. Each value is called an **element** and is accessed using its position, known as its **index**.

For example, rather than creating five separate variables:

```c
int value_0 = 100;
int value_1 = 101;
int value_2 = 102;
int value_3 = 103;
int value_4 = 104;
```

we can store the values in an array:

```c
int values[5] = { 100, 101, 102, 103, 104 };
```

All elements of an array have the same type and are stored next to each other in memory.

## Creating an array

To create an array, specify the type of its elements, its name and the number of elements in square brackets:

```c
type array_name[length];
```

For example:

```c
int readings[5];
```

This creates an array containing five integers.

The length must be large enough for all the values we want to store. Once the array has been created, its length cannot be changed.

## Initialising an array

Creating an array does not necessarily set its values:

```c
int readings[5];
```

If `readings` is a local variable, its elements initially contain indeterminate values. Do not assume they will be zero.

We can provide initial values when the array is created:

```c
int readings[5] = { 100, 101, 102, 103, 104 };
```

If we provide all the values, the compiler can work out the length:

```c
int readings[] = { 100, 101, 102, 103, 104 };
```

This creates an array of five integers.

We can also initialise all elements to zero:

```c
int readings[10] = { 0 };
```

This particular syntax sets the first element to zero and automatically initialises all remaining elements to zero as well.

The initialisation only happens when the array is created. This will not reset an existing array:

```c
readings = { 0 };     // Invalid C
```

We will look at how to reset or copy an array later on this page.

## Indexing an array

Array indices start at zero. In an array of length five, the valid indices are therefore `0` to `4`:

```c
int numbers[] = { 10, 20, 30, 40, 50 };

printf("%d\n", numbers[0]);    // First element: 10
printf("%d\n", numbers[2]);    // Third element: 30
printf("%d\n", numbers[4]);    // Fifth element: 50
```

For an array of length `n`:

- the first element is `array[0]`
- the second element is `array[1]`
- the final element is `array[n - 1]`

This is a common source of **off-by-one errors** which occur so often they have a long [wikipedia page](https://en.wikipedia.org/wiki/Off-by-one_error). An array with five elements does not have an element at index `5`.

We can use an index to update an element as well as read it:

```c
int scores[] = { 10, 20, 30 };

scores[1] = 25;

printf("%d\n", scores[1]);     // 25
```

## Processing an array with a loop

Arrays and `for` loops are often used together:

```c
#include <stdio.h>

int main(void)
{
    int readings[] = { 100, 101, 102, 103, 104 };

    for (int i = 0; i < 5; i++) {
        printf("readings[%d] = %d\n", i, readings[i]);
    }

    return 0;
}
```

The condition is `i < 5`, rather than `i <= 5`, because the final valid index is `4`.

We can also update every element using a loop:

```c
int values[5] = { 0 };

for (int i = 0; i < 5; i++) {
    values[i] = i * 10;
}
```

After the loop, the array contains:

```text
0 10 20 30 40
```

## Finding the number of elements

The `sizeof` operator gives the size of something in bytes.

For an array:

```c
int numbers[] = { 10, 20, 30, 40, 50 };
```

`sizeof(numbers)` gives the total size of the array, while `sizeof(numbers[0])` gives the size of one element.

Dividing the two gives the number of elements:

```c
size_t length = sizeof(numbers) / sizeof(numbers[0]);
```

We can then use `length` in the loop:

```c
#include <stdio.h>

int main(void)
{
    int numbers[] = { 10, 20, 30, 40, 50 };
    size_t length = sizeof(numbers) / sizeof(numbers[0]);

    for (size_t i = 0; i < length; i++) {
        printf("%d\n", numbers[i]);
    }

    return 0;
}
```

`size_t` is the type C uses for sizes. When printing a `size_t` value with `printf`, use `%zu`:

```c
printf("Length: %zu\n", length);
```

This method works while `numbers` is an array in the current scope. It does not work once the array has been passed to a function, because the function receives a pointer rather than the complete array. We will return to this when we cover pointers.

## Passing an array to a function

When passing an array to a function, also pass its length:

```c
#include <stdio.h>

void print_array(const int values[], size_t length)
{
    for (size_t i = 0; i < length; i++) {
        printf("%d\n", values[i]);
    }
}

int main(void)
{
    int readings[] = { 100, 101, 102, 103, 104 };
    size_t length = sizeof(readings) / sizeof(readings[0]);

    print_array(readings, length);

    return 0;
}
```

The function cannot work out the array length using `sizeof(values)`. It only receives information about where the first element is stored, so we must pass the length separately.

The `const` tells the compiler that `print_array` will not change the elements of the array.

## Two-dimensional arrays

A two-dimensional array can be used to represent a table or grid of values:

```c
int matrix[2][3] = {
    { 1, 2, 3 },
    { 4, 5, 6 }
};
```

The first index selects the row and the second selects the column:

```c
printf("%d\n", matrix[0][0]);    // 1
printf("%d\n", matrix[1][2]);    // 6
```

We can process the array using nested loops:

```c
#include <stdio.h>

int main(void)
{
    int matrix[2][3] = {
        { 1, 2, 3 },
        { 4, 5, 6 }
    };

    for (int row = 0; row < 2; row++) {
        for (int column = 0; column < 3; column++) {
            printf("%d ", matrix[row][column]);
        }

        printf("\n");
    }

    return 0;
}
```

The elements are stored row by row in memory:

```text
matrix[0][0]  matrix[0][1]  matrix[0][2]  matrix[1][0]  matrix[1][1]  matrix[1][2]
      1             2             3             4             5             6
```

We do not need the pointer explanation yet. For now, the important point is that `matrix[row][column]` accesses one value from the array.

## Common mistakes

I have done each of these about 100 times :D

### Accessing an element outside the array

C does not automatically check that an index is valid:

```c
int values[3] = { 10, 20, 30 };

printf("%d\n", values[5]);    // Invalid index
values[10] = 0;               // Invalid index
```

Both lines produce **undefined behaviour**. The program could read the wrong value, overwrite other data or crash.

The compiler may not catch this, particularly when the index is stored in a variable:

```c
values[index] = 0;
```

It is up to us to check that `index` is within the valid range:

```c
if (index >= 0 && index < 3) {
    values[index] = 0;
}
```

### Copying an array with `=`

You cannot copy one array to another using assignment:

```c
int source[3] = { 1, 2, 3 };
int destination[3];

destination = source;    // Invalid C
```

For a simple array, copy each element with a loop:

```c
for (int i = 0; i < 3; i++) {
    destination[i] = source[i];
}
```

The standard library also provides `memcpy`, but a loop is easier to understand and is sufficient for now.

### Hard-coded lengths

This loop works:

```c
for (int i = 0; i < 5; i++) {
    printf("%d\n", values[i]);
}
```

However, if the array length changes, we must remember to update every relevant loop.

Where possible, calculate the length once:

```c
size_t length = sizeof(values) / sizeof(values[0]);

for (size_t i = 0; i < length; i++) {
    printf("%d\n", values[i]);
}
```

### Forgetting the final valid index

For an array of length `n`, the final element is always:

```c
array[n - 1]
```

Accessing `array[n]` goes one element beyond the end of the array.