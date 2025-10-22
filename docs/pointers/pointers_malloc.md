---
title: Dynamic Memory
parent: Pointers
nav_order: 4
layout: default
---

# Dynamic Memory Allocation

Generally speaking, when we declare variables in C, their size must be known at compile time. However, there are situations where we may not know how much memory we need until the program is running. This is where **dynamic memory allocation** comes in. 

Memory can be divided into two main areas: the **stack** and the **heap**: 

## Memory overview: Stack vs Heap

- **Stack**
  - Region of memory for **automatic variables** (local variables inside functions).
  - Managed by the compiler: grows/shrinks as functions are called and return.
  - Very fast, but **size is limited** and lifetime ends when the function exits.
  - Up to now, this is what we have used for all our variables.

- **Heap**
  - Region of memory for **dynamic allocation** (`malloc`, `calloc`, `realloc`).
  - Managed by the programmer: you decide when to allocate and `free`.
  - Flexible size and lifetime, but slower and can fragment if misused.

**Mental model:**

- Stack = "temporary workspace" with automatic cleanup.
- Heap = "storage room" with manual management. We decide what to keep and when to release it.

## Why dynamic memory?

Static/stack memory has fixed size known at compile-time or function entry. **Dynamic memory** lets you **request memory at runtime** - when sizes depend on user input, file contents, or growing buffers. You get a pointer to a block on the **heap**, use it, and then **free** it when done.

---

## The four main functions: `malloc`, `calloc`, `realloc`, and `free`

### `void *malloc(size_t bytes);`

- Allocates **`bytes`** of uninitialized memory.
- Returns a pointer to the block, or **`NULL`** if it fails.
- Contents are **indeterminate** - you must initialize.

```c
int *a = malloc(10 * sizeof *a);  // 10 ints
if (!a) { /* handle OOM */ }
/* a[i] is garbage until you assign */
```

---

### `void *calloc(size_t n, size_t size);`
- Allocates **`n * size`** bytes and **zero-initializes** the block.
- Also returns **`NULL`** on failure.
- Useful when you want "all zeros" (e.g., for counters or to avoid uninitialized reads).

```c
double *x = calloc(12, sizeof *x);  // 12 doubles, all 0.0 bitwise (not NaN)
if (!x) { /* handle OOM */ }
```

> Note: "zeroed" means **all bits are 0**. For pointers, that yields a null pointer. For floats, it's +0.0.

---

### `void *realloc(void *ptr, size_t new_bytes);`
- **Resizes** a previously allocated block (`malloc/calloc/realloc`).
- If it **grows**, the allocator may **move** the block; the returned pointer may differ.
- If it **shrinks**, contents beyond the new size are discarded.
- On **failure**, returns **`NULL`** and **leaves the original pointer untouched**.

Safe pattern:

```c
size_t new_count = count * 2;
int *tmp = realloc(a, new_count * sizeof *a);
if (!tmp) {
    /* still have 'a' valid here */
    /* handle OOM without losing the pointer */
} else {
    a = tmp;     // take ownership of the resized block
    count = new_count;
}
```

To **free** using `realloc`, do: `realloc(ptr, 0)` *or* simply `free(ptr)`. Most code uses `free(ptr)` for clarity.

---

### `void free(void *ptr);`
- Releases the block back to the allocator.
- `free(NULL);` is a **no-op** (safe).
- After `free(p)`, the pointer becomes **invalid**; set it to `NULL` to avoid accidental reuse.

```c
free(a);
a = NULL;  // defensive
```

---

## General rules of thumb

1. **Check for `NULL` after allocation**  
   If `malloc` or `calloc` fails, it returns `NULL`. Always check before using the pointer.

2. **Use `sizeof *ptr` instead of `sizeof(type)`**  
   Example:  
   ```c
   int *p = malloc(n * sizeof *p); // safer if type changes
   ```

3. **Free everything you allocate (once)**  
   Every successful `malloc`/`calloc`/`realloc` needs exactly one `free`.  
   No free means a memory leak.  
   Free twice means a crash.

4. **`realloc` can move memory**  
   After `realloc`, the old pointer may be invalid. Only use the new one.

5. **`malloc` gives rubbish, `calloc` gives zeros**  
   Don’t read memory before you write to it.

6. **Don’t use memory after `free`**  
   Once freed, the pointer is invalid. Set it to `NULL` to avoid mistakes.

7. **Watch out for size overflow**  
   Before multiplying:  
   ```c
   if (n > SIZE_MAX / sizeof *p) { /* too big */ }
   ```

8. **Embedded tip**  
   Heap is small and can fragment. Prefer static arrays or fixed pools when possible. We will cover this more later in the course.
   
---

## Quick mental model

- `malloc`: **give me N bytes** (uninitialized).
- `calloc`: **give me N x size bytes and zero them**.
- `realloc`: **resize this block to M bytes** (may move).
- `free`: **I'm done with this block**.

---

### Example 1 - Read N, allocate an array, compute sum

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    size_t n;
    if (scanf("%zu", &n) != 1) return 1;

    if (n == 0) { puts("Nothing to do."); return 0; }

    if (n > SIZE_MAX / sizeof(int)) { fprintf(stderr, "size overflow\n"); return 1; }

    int *a = malloc(n * sizeof *a);
    if (!a) { perror("malloc"); return 1; }

    for (size_t i = 0; i < n; ++i) a[i] = (int)i;

    long long sum = 0;
    for (size_t i = 0; i < n; ++i) sum += a[i];

    printf("sum=%lld\n", sum);

    free(a);
    a = NULL;
    return 0;
}
```

---

## Common pitfalls & how to avoid them

Using dynamic memory incorrectly can lead to bugs, crashes, and security issues. Here are some common pitfalls with examples of how to avoid them.

---

## 1) Memory Leak

**Problem:** Allocate but never free -> memory is lost.

**Bad**
```c
#include <stdlib.h>

int main(void) {
    int *p = malloc(100 * sizeof *p); // allocated
    if (!p) return 1;
    // ... use p ...
    return 0; // OOPS: no free -> leak
}
```

**Good**
```c
#include <stdlib.h>

int main(void) {
    int *p = malloc(100 * sizeof *p);
    if (!p) return 1;

    // ... use p ...

    free(p);     // release the memory
    p = NULL;    // optional: prevent accidental reuse
    return 0;
}
```

---

## 2) Double Free
**Problem:** Free the same pointer twice -> undefined behaviour/crash.

**Bad**
```c
#include <stdlib.h>

int main(void) {
    int *p = malloc(10 * sizeof *p);
    if (!p) return 1;

    free(p);
    free(p); // OOPS: double free
    return 0;
}
```

**Good (set to NULL after free)**
```c
#include <stdlib.h>

int main(void) {
    int *p = malloc(10 * sizeof *p);
    if (!p) return 1;

    free(p);
    p = NULL;     // now safe: free(NULL) is a no-op
    free(p);      // does nothing
    return 0;
}
```

---

## 3) Use-After-Free
**Problem:** Using a pointer after it's been freed.

**Bad**
```c
#include <stdlib.h>
#include <stdio.h>

int main(void) {
    int *p = malloc(sizeof *p);
    if (!p) return 1;

    *p = 42;
    free(p);
    printf("%d\n", *p); // OOPS: use-after-free
    return 0;
}
```

**Good (nullify and stop using)**
```c
#include <stdlib.h>
#include <stdio.h>

int main(void) {
    int *p = malloc(sizeof *p);
    if (!p) return 1;

    *p = 42;
    printf("%d\n", *p);

    free(p);
    p = NULL;    // prevents accidental reuse
    // Do not touch p anymore.
    return 0;
}
```

---

## 4) Buffer Overflow
**Problem:** Writing past the end of the allocated array.

**Bad**
```c
#include <stdlib.h>

int main(void) {
    size_t n = 5;
    int *a = malloc(n * sizeof *a);
    if (!a) return 1;

    for (size_t i = 0; i <= n; ++i) { // OOPS: i goes 0..5 (6 writes)
        a[i] = (int)i;
    }

    free(a);
    return 0;
}
```

**Good (use proper bounds)**
```c
#include <stdlib.h>

int main(void) {
    size_t n = 5;
    int *a = malloc(n * sizeof *a);
    if (!a) return 1;

    for (size_t i = 0; i < n; ++i) { // i goes 0..4
        a[i] = (int)i;
    }

    free(a);
    return 0;
}
```

---

## 5) Size Overflow
**Problem:** `n * sizeof(T)` overflows `size_t`, allocating less than you think.

**Bad**
```c
#include <stdlib.h>

int main(void) {
    size_t n = (size_t)-1 / 2;   // huge
    int *a = malloc(n * sizeof *a); // may overflow internally
    if (!a) return 1; // or worse: succeeds but too small!
    free(a);
    return 0;
}
```

**Good (check before multiply)**
```c
#include <stdlib.h>
#include <stdio.h>

int main(void) {
    size_t n = (size_t)1 << 62;  // example large input
    if (n > SIZE_MAX / sizeof(int)) {
        fprintf(stderr, "requested size too large\n");
        return 1;
    }
    int *a = malloc(n * sizeof *a);
    if (!a) {
        fprintf(stderr, "out of memory\n");
        return 1;
    }
    free(a);
    return 0;
}
```

---

## 6) Aliasing with `realloc`
**Problem:** Keeping extra pointers (aliases) to a block that may move after `realloc`.

**Bad**

```c
#include <stdlib.h>

int main(void) {
    int *a = malloc(4 * sizeof *a);
    if (!a) return 1;
    int *alias = a; // second pointer to same block

    int *tmp = realloc(a, 8 * sizeof *a); // block may move
    if (!tmp) { free(a); return 1; }
    a = tmp;

    alias[0] = 123; // OOPS: alias may point to the OLD location
    free(a);
    return 0;
}
```

**Good (single owner pointer; update after `realloc`)**

```c
#include <stdlib.h>

int main(void) {
    int *a = malloc(4 * sizeof *a);
    if (!a) return 1;

    int *tmp = realloc(a, 8 * sizeof *a);
    if (!tmp) { free(a); return 1; }
    a = tmp; // only one authoritative pointer

    a[0] = 123; // safe
    free(a);
    return 0;
}
```