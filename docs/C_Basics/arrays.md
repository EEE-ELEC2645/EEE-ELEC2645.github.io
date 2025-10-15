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

## Introduction

A single variable represents one location in memory. An **array** is a collection of variables of the same type, stored in **contiguous memory**, meaning each element is stored one after another, with no gaps. Arrays let you store and process multiple values under one name, using an **index** to access each element.

Important things to remember:

- Arrays in C are **zero-indexed**: the first element is index `0`.
- All elements are stored next to each other in memory.
- All elements must be of the same type.

---

## Why arrays matter

Arrays help you:

- Store multiple values without creating separate variables
- Process data efficiently using loops
- Pass groups of values to functions

---

## Static arrays

A **static array** is an array whose size is set at **compile time**, and it cannot change while the program runs. You need to tell the compiler how many elements the array will have, either by giving a number or by listing the initial values.

We create an array by first specifying the type e.g. `int`, give the array a name and then use square brackets `[]`.

```c
type arrayName[size];
```

This creates the memory for the array `arrayName` but it does not set any values, they could be zero or whatever junk values which were already there before the program was run. Telling the compiler what values should be in the array at the start is what is known as *initialisation*, it is good practice to always do this! 

### Example: create and print an array

Here we are creating an array specifying both the size and initialising each of the values:

```c
#include <stdio.h>

int main(void) {
  // Initialise an array with 5 integers
  int array[5] = { 100, 101, 102, 103, 104 };

  // Print each element using a for loop
  for (int i = 0; i < 5; i++) {
    printf("array[%d] = %d\n", i, array[i]);
  }
  return 0;
}
```

### Create all

If you provide initial values, you can omit the size:

  ```c
  int array[] = { 100, 101, 102, 103, 104 };
  ```

This will make an array of 5 elements.

### Initialise all values to zero

We can set all values of an array to zero, but only when it is created.

```c
int zeros[10] = { 0 };  // all elements set to 0
```

---

## Indexing arrays

**Indexing** an array means accessing elements by their position, starting from index `0`. For an array `arr[n]`, the first element is `arr[0]` and the last is `arr[n-1]`.

For example:

```c
#include <stdio.h>

int main(void) {
    int numbers[] = {10, 20, 30, 40, 50};

    printf("First element: %d\n", numbers[0]); // 10
    printf("Third element: %d\n", numbers[2]); // 30
    printf("Last element: %d\n", numbers[4]);  // 50

    return 0;
}
```

*Note* We have to keep track ourselves what the index to the final element of the array is!

---

## Determining the Number of Elements in an Array Using `sizeof`

Sometimes we need to know how many elements an array contains. For arrays whose size is known at **compile time**, we can use the `sizeof` operator. This operator returns the size of a variable **in bytes**.

```c
#include <stdio.h>

int main(void) {
    int nums[] = { 1, 2, 3, 4, 5 };
    size_t length = sizeof(nums) / sizeof(nums[0]);
    printf("Array length = %zu\n", length); // prints "Array length = 5"
    return 0;
}
```

### How it works

- `sizeof(nums)` gives the total size of the array in bytes (e.g., 5 `int`s × 4 bytes = 20 bytes on most systems).
- `sizeof(nums[0])` gives the size of one element (e.g., 4 bytes for an `int`).
- Dividing these values gives the number of elements:  
  `sizeof(nums) / sizeof(nums[0])`.

#### Important notes

- This method **only works for arrays in the same scope where they are declared**.  
  If you pass the array to a function, it decays to a pointer, and `sizeof` will return the size of the pointer instead.
- It does **not** work for dynamically allocated memory (e.g., arrays created with `malloc`), because those are accessed through pointers.
- The type `size_t` is the correct type for sizes returned by `sizeof`. Use `%zu` in `printf` when printing `size_t` values.

---

## 2D array (matrix)

A 2D array in C is essentially an array of arrays, often used to represent a matrix or table of values. The example below creates a 2×3 matrix and prints its elements using nested loops.

```c
#include <stdio.h>

int main(void) {
  int matrix[2][3] = {
    { 1, 2, 3 },
    { 4, 5, 6 }
  };

  for (int i = 0; i < 2; i++) {
    for (int j = 0; j < 3; j++) {
      printf("%d ", matrix[i][j]);
    }
    printf("\n");
  }
  return 0;
}
```

In C, **2D arrays are stored in row-major order**, which means all elements of the first row are stored consecutively in memory, followed by all elements of the second row, and so on. For example, in a `2x3` array:

```c
matrix[2][3] = { {1, 2, 3}, {4, 5, 6} };
```

the memory layout is:

```c
1  2  3  4  5  6
```

This is why `matrix[i][j]` is actually accessed internally in the code with some pointer magic like this: `*(matrix[i] + j)` . The entire array is a single contiguous block of memory, so you can also treat it as a flat array if needed.

---

## Common pitfalls with Arrays


### 1) Arrays are **not assignable** (except at initialisation)

```c
int a[3] = {1,2,3};
int b[3];
b = a;              // BUG: invalid in C
```

**Fix**: Copy with a loop or `memcpy` (for POD types).

```c
for (int i = 0; i < 3; i++) b[i] = a[i]; // this is essentially what happens in the background anyway
/* or: memcpy(b, a, sizeof a); */
```

### 2) Hard coded lengths

Hard-coding lengths (e.g., `for (i=0; i<5; i++)`) makes maintenance risky. What happens if we decide to change the length of the array? If we had lots of for loops We could easily forget to change all of them.

**Fix**: Compute or name the length once and reuse it.

```c
int a[] = { 2, 3, 5, 7, 11 };
const size_t LEN = sizeof a / sizeof a[0]; // const here means that LEN can never be changed, use uppercase for this
for (size_t i = 0; i < LEN; i++) { /* ... */ }
```

---

### 3) Indexing beyond the array bounds

In C, **there is no automatic bounds checking**. If you access an index outside the valid range (e.g., `arr[5]` in an array of length 5), the compiler won’t stop you, but the behaviour is **undefined**. This can lead to corrupted data, crashes, or subtle bugs.

```c
int a[3] = {1, 2, 3};
printf("%d\n", a[5]); // Undefined behaviour, we don't know what this could be reading
a[10] = 0 ; // even worse! We could be overwriting something very important
```

---

### 4) Arrays decay to pointers in functions

When you pass an array to a function, it **decays to a pointer** to its first element. This means the function does not know the array’s length unless you pass it explicitly.

Example:

```c
void printArray(int arr[], size_t len) {
    for (size_t i = 0; i < len; i++) {
        printf("%d ", arr[i]);
    }
}

int main(void) {
    int nums[] = {1, 2, 3, 4};
    printArray(nums, sizeof nums / sizeof nums[0]); // must pass length
}
```

*(We’ll look at this in more detail later when we look at pointers)*

---

### Things to remember

- Arrays in C have **fixed size**; you cannot resize them after creation.
- Array names often **decay** to pointers in expressions and function parameters.
- Always track lengths to avoid out-of-bounds access.

---

## Summary

- Arrays store multiple values of the same type in contiguous memory.
- Use loops to process arrays, and pass length explicitly to functions.
- Watch out for common pitfalls: off-by-one, `sizeof` on parameters, string terminators, non-assignable
  arrays, `memset` misuse, 2D parameter shapes, VLAs, signed/unsigned issues, and returning arrays.

---

