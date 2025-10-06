---
title: Arrays
parent: C Basics
nav_order: 6
layout: default
---


# Arrays in C

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

- If you provide initial values, you can omit the size:

  ```c
  int array[] = { 100, 101, 102, 103, 104 };
  ```




### Initialise all values to zero

We can set all values of an array to zero, but only when it is created.

```c
int zeros[10] = { 0 };  // all elements set to 0
```

---

### Compute length using `sizeof` (at the point of declaration)

```c
#include <stdio.h>

int main(void) {
  int nums[] = { 1, 2, 3, 4, 5 };
  size_t length = sizeof(nums) / sizeof(nums[0]);
  printf("Array length = %zu\n", length);
  return 0;
}
```

The variable `length` is calculated using the `sizeof` operator. `sizeof(nums)` gives the total number of bytes used by the entire array, while `sizeof(nums[0])` gives the number of bytes used by a single element. Dividing these gives the number of elements in the array:

- `sizeof(nums)` returns the size in bytes of the whole array (for 5 `int`s, typically 20 bytes if `int` is 4 bytes).
- `sizeof(nums[0])` returns the size in bytes of one element (typically 4 bytes for `int`).
- So, `sizeof(nums) / sizeof(nums[0])` = total bytes / bytes per element = number of elements.

This method only works for arrays whose size is known at compile time (i.e., not for pointers or function parameters).


The variable length in your code represents the number of elements in the array nums.
Here’s why:

sizeof(nums) gives the total size of the array in bytes.
sizeof(nums[0]) gives the size of one element (in this case, an int).
Dividing the two gives the count of elements:

length=total size of arraysize of one element=sizeof(nums)sizeof(nums[0])\text{length} = \frac{\text{total size of array}}{\text{size of one element}} = \frac{\text{sizeof(nums)}}{\text{sizeof(nums[0])}}length=size of one elementtotal size of array​=sizeof(nums[0])sizeof(nums)​
For your array:

nums[] = {1, 2, 3, 4, 5} → 5 elements.
On most systems, sizeof(int) = 4 bytes.
So sizeof(nums) = 5×4=205 \times 4 = 205×4=20 bytes.
sizeof(nums[0]) = 4 bytes.
Therefore, length = 20 / 4 = 5.

The size_t type is used because sizeof returns an unsigned integral type suitable for representing sizes.




### 2D array (matrix)

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


### 4) Arrays are **not assignable** (except at initialisation)

```c
int a[3] = {1,2,3};
int b[3];
b = a;              // BUG: invalid in C
```
**Fix**: Copy with a loop or `memcpy` (for POD types).
```c
for (int i = 0; i < 3; i++) b[i] = a[i];
/* or: memcpy(b, a, sizeof a); */
```


### 8) Signed/unsigned index mismatches

`sizeof` yields a `size_t` (unsigned). Mixing `int i` with `size_t length` can raise warnings or logic
issues.

```c
size_t length = 5;
for (int i = length - 1; i >= 0; i--) {  // BUG: i >= 0 is always true after wrap
  /* ... */
}
```
**Fix**: Use `size_t` for indices, or structure the loop differently.
```c
for (size_t i = 0; i < length; i++) { /* ... */ }
```


### 10) Magic lengths sprinkled in code

Hard-coding lengths (e.g., `for (i=0; i<5; i++)`) makes maintenance risky.

**Fix**: Compute or name the length once and reuse it.
```c
int a[] = { 2, 3, 5, 7, 11 };
const size_t LEN = sizeof a / sizeof a[0];
for (size_t i = 0; i < LEN; i++) { /* ... */ }
```

---

## Things to remember

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

