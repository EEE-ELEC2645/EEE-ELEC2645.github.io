---
title: Dynamic Memory 
parent: Pointers
nav_order: 4
layout: default
---

# Dynamic Memory Allocation with `malloc`, `calloc`, `realloc`, and `free`

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

- Stack = “temporary workspace” with automatic cleanup.
- Heap = “storage room” with manual management. We decide what to keep and when to release it.

## Why dynamic memory?

Static/stack memory has fixed size known at compile-time or function entry. **Dynamic memory** lets you **request memory at runtime** - when sizes depend on user input, file contents, or growing buffers. You get a pointer to a block on the **heap**, use it, and then **free** it when done.

---

## The four main calls

### `void *malloc(size_t bytes);`

- Allocates **`bytes`** of uninitialized memory.
- Returns a pointer to the block, or **`NULL`** if it fails.
- Contents are **indeterminate**—you must initialize.

```c
int *a = malloc(10 * sizeof *a);  // 10 ints
if (!a) { /* handle OOM */ }
/* a[i] is garbage until you assign */
```

---

### `void *calloc(size_t n, size_t size);`
- Allocates **`n * size`** bytes and **zero-initializes** the block.
- Also returns **`NULL`** on failure.
- Useful when you want “all zeros” (e.g., for counters or to avoid uninitialized reads).

```c
double *x = calloc(12, sizeof *x);  // 12 doubles, all 0.0 bitwise (not NaN)
if (!x) { /* handle OOM */ }
```

> Note: “zeroed” means **all bits are 0**. For pointers, that yields a null pointer. For floats, it’s +0.0.

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
- `free(NULL);` is a **no‑op** (safe).
- After `free(p)`, the pointer becomes **invalid**; set it to `NULL` to avoid accidental reuse.

```c
free(a);
a = NULL;  // defensive
```

---

## Essential rules of thumb

1. **Always check for `NULL`** after `malloc/calloc/realloc`.
2. **Use `sizeof *ptr` instead of `sizeof(type)`** to avoid mismatches:
   ```c
   Foo *p = malloc(n * sizeof *p);  // safer if Foo changes later
   ```
3. **One `free` per successful allocation**—no more, no less (avoid double-free and leaks).
4. Assume **`realloc` can move memory**. Don’t keep other raw aliases to the old pointer.
5. Don’t read uninitialized memory: `malloc` gives **garbage**, `calloc` gives **zeros**.
6. Don’t access after free (**use‑after‑free**). Null the pointer after freeing.
7. Beware **integer overflow** in size calculations:
   ```c
   if (n > SIZE_MAX / sizeof *p) { /* handle overflow */ }
   p = malloc(n * sizeof *p);
   ```
8. Within embedded constraints: dynamic allocation may be limited or fragmented—prefer fixed pools or bounded alloc strategies when possible.

---

## Quick mental model

- `malloc`: **give me N bytes** (uninitialized).
- `calloc`: **give me N×size bytes and zero them**.
- `realloc`: **resize this block to M bytes** (may move).
- `free`: **I’m done with this block**.

---

### Example 1 — Read N, allocate an array, compute sum

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

- **Leak:** forget to `free` → use structured ownership (one owner, clear `free` path).
- **Double free:** `free` twice → set pointer to `NULL` after `free` and guard.
- **Use‑after‑free:** reading/writing after `free` → set to `NULL`; separate “destroy” from “use” phases.
- **Buffer overflow:** indexing beyond `n` → track and check lengths; encapsulate in a small API.
- **Size overflow:** `n * sizeof(T)` wraps → check `n > SIZE_MAX / sizeof(T)`.
- **Aliasing + `realloc`:** storing old pointers elsewhere → centralize access through one owner pointer.

---
