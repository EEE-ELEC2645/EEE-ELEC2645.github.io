---
title: Arrays with Pointers
parent: Pointers
nav_order: 2
layout: default
---

# Arrays and Pointers

## Introduction

A variable represents a single location in memory. A collection of variables in a **contiguous** address space (i.e no gaps) is known as an **array**. Arrays and pointers are closely related in C because array names frequently **decay** to pointers (more on this below).

A conceptual diagram of an `int` array with 7 elements named `array`:

```
array:
[ array[0] ][ array[1] ][ array[2] ][ array[3] ][ array[4] ][ array[5] ][ array[6] ]
      ^         ^           ^           ^           ^           ^           ^
   &array[0] &array[1]   &array[2]   &array[3]   &array[4]   &array[5]   &array[6]
```

Arrays are **zero‑indexed**: the first element is `array[0]`.

---

## Arrays and pointers

Arrays and pointers are linked because array names **often convert to** pointers to their first element in expressions. You can index arrays with `[]` or use pointer arithmetic with `*` and `+`.

```c
#include <stdio.h>

int main(void) {
    int array[5] = {100, 101, 102, 103, 104};
    // Print values and their addresses
    for (int i = 0; i < 5; i++) {
        printf("array[%d] = %d\n", i, array[i]);
    }
    for (int i = 0; i < 5; i++) {
        printf("address of array[%d] = %p\n", i, &array[i]);
    }
    // 'array' in an expression is equivalent to &array[0]
    printf("array (decays to &array[0]) = %p\n", array);
    printf("&array[0]                  = %p\n", &array[0]);
    // Use pointers to access array elements
    for (int i = 0; i < 5; i++) {
        printf("*(array + %d) = %d\n", i, *(array + i));
    }
    return 0;
}
```

**What to notice**

- Consecutive element addresses increase by `sizeof(int)`.
- `array` (in most expressions) is the same address as `&array[0]`.
- `array[i]` is the same as `*(array + i)`.

**Important exceptions (no decay):**

- `sizeof array` gives the **total bytes** of the array object (e.g., `5 * sizeof(int)`), not a pointer size.
- `&array` gives type **pointer to the entire array** (e.g., `int (*)[5]`), not `int *`.
- In the **declaration** itself, `int array[5];` is an array, not a pointer.

---

## Passing arrays to functions

Functions can accept arrays and can modify them. However, in a function parameter list, `int array[]` is **rewritten** by the compiler as `int *array`. That means the function receives a pointer, so you must pass the length separately if needed.

```c
#include <stdio.h>

// Read-only access (note 'const')
int sum_of_array(const int array[], int n) {
    int sum = 0;
    for (int i = 0; i < n; i++) {
        sum += array[i];
    }
    return sum;
}

// Modifying access (no 'const')
void square_array(int array[], int n) {
    for (int i = 0; i < n; i++) {
        array[i] = array[i] * array[i];
    }
}

int main(void) {
    int array[5] = {1, 2, 3, 4, 5};

    int sum = sum_of_array(array, 5); // 'array' decays to &array[0]
    printf("Sum = %d\n", sum);

    square_array(array, 5);            // modifies the original array
    for (int i = 0; i < 5; i++) {
        printf("array[%d] = %d\n", i, array[i]);
    }

    sum = sum_of_array(array, 5);
    printf("New sum = %d\n", sum);

    return 0;
}
```

**Key points**

- Inside `sum_of_array` and `square_array`, the parameter `array` is actually a pointer (`int *array`).
- Therefore, `sizeof array` **inside the function** would give the pointer size (e.g., 8 on 64‑bit), **not** the number of elements.
- Use `const` to make read‑only intent explicit and enforced by the compiler.

**Sample output**
```
Sum = 15
array[0] = 1
array[1] = 4
array[2] = 9
array[3] = 16
array[4] = 25
New sum = 55
```

---

## (Optional) Multi‑dimensional arrays

For `int m[R][C];`, the array decays **one level**: `m` decays to `int (*)[C]` (pointer to an array of `C` ints). The **rightmost dimension must be known** at the call site to index correctly.

```c
#include <stdio.h>

void zero2d(int (*mat)[4], int rows) {     // pointer to array of 4 ints
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < 4; c++) {
            mat[r][c] = 0;
        }
    }
}

int main(void) {
    int m[3][4] = { {1,2,3,4}, {5,6,7,8}, {9,10,11,12} };
    zero2d(m, 3);  // 'm' decays to pointer to row (int (*)[4])

    for (int r = 0; r < 3; r++) {
        for (int c = 0; c < 4; c++) {
            printf("%d ", m[r][c]);
        }
        printf("\n");
    }
    return 0;
}
```

---

## Common pitfalls

- **`sizeof` trap in callees:**  
  Inside a function parameter declared as `int a[]` (really `int *a`), `sizeof a` is pointer size, **not** the array length. Compute lengths in the caller:
  ```c
  #define LEN(x) (sizeof(x)/sizeof((x)[0]))
  ```
- **Confusing `a` vs `&a`:**  
  `a` (in expressions) → pointer to first element (`T*`).  
  `&a` → pointer to the **entire array** (`T (*)[N]`).
- **Pointer arithmetic off‑by‑one:**  
  End pointer is `a + N`. Valid indices are `0..N-1`; never dereference `a + N`.
- **Const correctness:**  
  Use `const T *` parameters for read‑only functions to prevent accidental modification.

---

## Summary

- An array is a contiguous block of elements with a fixed size; a pointer is a variable holding an address.  
- In **most expressions** (including function calls), an array name **decays** to a pointer to its first element.  
- Because of decay, functions that take “arrays” actually receive pointers—so also pass the **length** (or otherwise provide it).  
- `sizeof a` and `&a` are the two primary places you’ll see **no decay** and different types/values.
