---
title: Resizing dynamic memory
parent: Pointers
nav_order: 6
layout: default
---

# Resizing dynamic memory

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

The previous page introduced `malloc()` and `free()`. C also provides `calloc()` for allocating zeroed memory and `realloc()` for changing the size of an existing allocation.

These functions are also provided by:

```c
#include <stdlib.h>
```

## Allocating zeroed memory with `calloc()`

`calloc()` allocates space for several elements and sets all the allocated bytes to zero:

```c
int *values = calloc(count, sizeof(*values));
```

The two arguments are:

- the number of elements
- the size of each element

As with `malloc()`, always check whether the allocation succeeded:

```c
if (values == NULL) {
    return 1;
}
```

The memory must still be released using:

```c
free(values);
```

## Resizing memory with `realloc()`

`realloc()` changes the size of an existing allocation:

```c
int *resized_values =
    realloc(values, new_count * sizeof(*values));
```

The allocated block may be moved to a new address, so `realloc()` returns a pointer to the resized memory.

Do not assign the result directly back to the original pointer:

```c
values = realloc(values, new_count * sizeof(*values)); // dont do this!
```

If the allocation fails, `realloc()` returns `NULL`. Assigning it directly would lose the address of the original memory.

Use a temporary pointer instead:

```c
int *resized_values =
    realloc(values, new_count * sizeof(*values));

if (resized_values == NULL) {
    free(values);
    return 1;
}

values = resized_values;
```

After a successful call, use the new pointer:

```c
values = resized_values;
```

The original pointer should not be used separately because the memory may have moved.

## Memory added by `realloc()`

If the allocation becomes larger, the new part is not initialised:

```c
size_t old_count = 5;
size_t new_count = 10;
```

After resizing, initialise the new elements before reading them:

```c
for (size_t i = old_count; i < new_count; i++) {
    values[i] = 0;
}
```

## Embedded systems

Repeatedly allocating and resizing memory can fragment a small heap.

For embedded systems, it is often safer to reserve a fixed-size buffer based on the maximum amount of data the program should store:

```c
#define BUFFER_SIZE 100

int values[BUFFER_SIZE];
size_t value_count = 0;
```

The memory use is fixed and known in advance. If the buffer becomes full, the program can reject new data, process and clear the existing data, or overwrite the oldest values.

A buffer that overwrites its oldest entries is known as a circular buffer. We will use this idea later for continuously arriving data.

Use dynamic allocation when the runtime flexibility is genuinely needed. For many microcontroller programs, a fixed-size buffer gives more predictable behaviour.