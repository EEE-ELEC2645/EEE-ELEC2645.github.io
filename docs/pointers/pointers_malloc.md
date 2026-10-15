---
title: Dynamic memory basics
parent: Pointers
nav_order: 5
layout: default
---

# Dynamic memory basics

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

So far, the size of our arrays has been fixed when the program is written:

```c
int values[10];
```

Sometimes we do not know how much memory is needed until the program is running.

For example, the number of values might come from:

- user input
- a file
- data received from another system

Dynamic memory allocation allows the program to request memory while it is running.

## The stack and heap

The memory used by a program is divided into several areas. Two that we need to know about are the stack and the heap.

Local variables normally use the stack:

```c
void example(void)
{
    int value = 10;
    int numbers[20];
}
```

Allocating local variables on the stack is normally fast and their memory is released automatically when the function returns. Allocating memory on the heap using `malloc()` has some additional overhead, and the program must later release the memory using `free()`.

A simple way to think about the difference is:

```text
Stack: automatic, normally fast, fixed lifetime
Heap:  flexible size and lifetime, manually managed
```

You can see that the flexibility of using the Heap comes at the cost of us having to control the memory ourselves - massively increasing the chance of us doing something wrong!

## Allocating memory with `malloc()`

The `malloc()` function requests a block of memory from the heap, and is provided by `<stdlib.h>`. For example, this allocates enough space for ten integers:

```c
#include <stdlib.h>
int *values = malloc(10 * sizeof(int));
```

`malloc()` returns the address of the allocated memory. We store that address in a pointer.

Using `sizeof` makes sure the correct number of bytes is requested:

```c
10 * sizeof(int)
```

A common alternative is (although I find it quite confusing tbh):

```c
int *values = malloc(10 * sizeof(*values));
```

Here, `sizeof(*values)` means the size of the type that `values` points to. This avoids repeating `int`.

## Checking that allocation succeeded

Memory allocation can fail. If `malloc()` cannot provide the requested memory, it returns `NULL`.

Just like we saw with pointers or file pointers, always check the result before using the memory:

```c
int *values = malloc(10 * sizeof(*values));

if (values == NULL) {
    return 1;
}
```

Do not access the memory unless the pointer has been checked, it could cause crashes and chaos.

## Using the allocated memory

The allocated block can be used like an array:

```c
for (size_t i = 0; i < 10; i++) {
    values[i] = (int)i;
}
```

The memory returned by `malloc()` is not initialised. It may contain old values left in that part of memory.

Set each element before reading it:

```c
for (size_t i = 0; i < 10; i++) {
    values[i] = 0;
}
```

## Releasing memory with `free()`

Memory allocated using `malloc()` remains allocated until it is released using `free()`:

```c
free(values);
```

After calling `free()`, the memory no longer belongs to the program.

Do not use the pointer to access that memory again:

```c
free(values);

values[0] = 10;    // Invalid
```

It can be useful to set the pointer to `NULL` after freeing it:

```c
free(values);
values = NULL;
```

This does not change what `free()` does, but it makes accidental reuse easier to spot. Basically, protect yourself from yourself.

## Complete example

This program asks how many values are required, allocates an array and fills it with numbers:

```c
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    size_t count;

    printf("How many exciting numbers do you need? ");

    if (scanf("%zu", &count) != 1 || count == 0) {
        printf("Invalid number\n");
        return 1;
    }

    int *values = malloc(count * sizeof(*values));

    if (values == NULL) {
        printf("Could not allocate memory\n");
        return 1;
    }

    for (size_t i = 0; i < count; i++) {
        values[i] = (int)(i + 1);
    }

    for (size_t i = 0; i < count; i++) {
        printf("values[%zu] = %d\n", i, values[i]);
    }

    free(values);
    values = NULL;

    return 0;
}
```

The amount of memory depends on the value entered while the program is running:

```c
malloc(count * sizeof(*values))
```

The program follows the main dynamic-memory sequence:

```text
allocate -> check -> use -> free
```

## Common mistakes

I have made each of these several times.

### Memory leak

A memory leak occurs when memory is allocated but never freed:

```c
int *values = malloc(10 * sizeof(*values));

if (values == NULL) {
    return 1;
}

// Use values

return 0;    // values was not freed
```

The memory remains allocated until the program ends.

Release it when it is no longer needed:

```c
free(values);
```

### Using memory after `free()`

After freeing the memory, the pointer must not be dereferenced:

```c
free(values);

printf("%d\n", values[0]);    // Invalid
```

The pointer still contains an address, but the memory at that address no longer belongs to the program.

### Freeing memory twice

Do not free the same allocation twice:

```c
free(values);
free(values);    // Invalid
```

Setting the pointer to `NULL` after freeing can help:

```c
free(values);
values = NULL;
```

Calling `free(NULL)` is safe.

## Dynamic memory in embedded systems

Dynamic memory can be useful, but it is often avoided or used *very* carefully on microcontrollers. The available heap may be small, like 10 kB small, and repeatedly allocating and freeing blocks can fragment the memory.

For embedded systems, it is normally safer to allocate a fixed-size array large enough for the maximum amount of data we expect:

```c
#define MAX_VALUES 100

int values[MAX_VALUES];
size_t value_count = 0;
```

The array always has space for 100 values, while `value_count` records how many elements are currently being used.

If the array becomes full, the program must decide what to do. It could reject new values, process and clear the existing values, or overwrite the oldest value. The important point is that the maximum memory use is known when the program is compiled.

Dynamic memory is still useful in some situations, but fixed-size buffers often give more predictable behaviour on a microcontroller.

The next page covers `calloc()`, resizing memory with `realloc()`, and managing growing arrays.

## How other languages manage memory

C gives the programmer direct control over dynamic memory using `malloc()` and `free()`.

Other languages may manage this automatically. For example, Java, Python and C# reclaim unused memory using automatic memory-management systems. Rust uses ownership rules checked by the compiler, while modern C++ often uses objects that release their memory automatically.

C gives us more direct control, but also makes us responsible for avoiding memory leaks, double frees and use-after-free errors. Meaning many more chances for segmentation faults, hooray!