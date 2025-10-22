---
title: Pointer Basics
parent: Pointers
nav_order: 1
layout: default
---

# Pointer Basics

## Introduction

Before diving into pointers, it’s worth recapping simple variables. A variable represents a location in memory (RAM). Memory is organised into bytes (8 bits), each with a unique address. As there are many addresses (for example, a 32-bit machine has 2³² possible addresses), we usually write them in hexadecimal (from `0x00000000` upwards).

When we talk about an 8-bit or 32-bit machine, we’re referring to the *word size*. You can imagine RAM as rows of bytes, where the “row width” is the word size. On a 32-bit system, many 32-bit types (like `int` or `float`) are naturally aligned to 4-byte boundaries. That is why their addresses are often multiples of 4. Aligning data helps the CPU load and store efficiently.

### Memory addresses & pointers — at a glance

| Concept | What it is | Example | Why it matters |
|---|---|---|---|
| **Memory address** | A number that identifies a byte in RAM. Usually shown in hex. | `0x20001000`, `0x7FFC1234ABCD` | Lets the program find where data lives. |
| **Variable** | A named region of memory with a type and size. | `int temperature = 26;` | The compiler knows how many bytes to reserve and how to interpret them. |
| **Pointer** | A variable that stores a **memory address**. Its *type* tells C what it points to. | `int *p = &temperature;` | Using `*p` accesses the `int` stored at the address in `p`. |
| **Address-of (`&`)** | Operator that yields the address of an object. | `&temperature` | Used to initialise pointers or pass by address. |
| **Dereference (`*`)** | Operator to access the object a pointer points to. | `*p = 30;` | Reads/writes the target object via its address. |
| **`%p` printing** | `printf` format for addresses; cast to `(void *)`. | `printf("%p", (void*)p);` | Portable, well-defined way to print addresses. |
| **Pointer arithmetic** | Advances in units of the pointed-to type. | If `int *p; p+1` moves `sizeof(int)` bytes | Correct stepping through arrays/blocks of typed data. |
| **Null pointer** | “Points nowhere” sentinel value. | `int *p = NULL;` | Safe initial state; check before dereferencing. |

---

## Pointers in C

A *pointer* stores a memory address. In C, we use the address-of operator `&` to get an address, and the dereference operator `*` to access the object at that address.

### Example 0 — The smallest useful demo

```c
// pointer_min_demo.c
#include <stdio.h>

int main(void)
{
    int x = 5;          // a normal variable
    int *p = &x;        // p stores the address of x

    printf("x = %d\n", x);
    printf("&x = %p, p = %p\n", (void *)&x, (void *)p);

    *p = 42;            // write to x via the pointer
    printf("x after *p = 42 -> %d\n", x);

    return 0;
}
```

**What’s happening?**  
`p` holds the address of `x`. Using `*p` lets you access (and modify) the same memory that `x` occupies. The printed addresses for `&x` and `p` are the same because they refer to the same location.

---

### Example 1 — Pointer to a `float`

```c
// pointer_float.c
#include <stdio.h>
#include <stddef.h>  // for NULL

int main(void)
{
    float temperature = 21.5f;   // a float variable
    float *pf = &temperature;    // pointer to float

    printf("temperature = %.2f\n", temperature);
    printf("&temperature = %p, pf = %p\n", &temperature, pf);

    // read via pointer
    printf("*pf (read) = %.2f\n", *pf);

    // write via pointer
    *pf = 23.0f;
    printf("temperature after *pf = 23.0f -> %.2f\n", temperature);

    // demonstrate pointer arithmetic with float (moves by sizeof(float))
    float values[3] = {1.0f, 2.0f, 3.0f};
    float *q = values;           // points to values[0]
    printf("q   = %p -> %.1f\n", q,   *q);
    printf("q+1 = %p -> %.1f\n", (q+1), *(q+1));
    printf("q+2 = %p -> %.1f\n", (q+2), *(q+2));

    // null pointer safety check example
    pf = NULL;
    if (pf == NULL) {
        printf("pf is NULL (safe to test, unsafe to dereference).\n");
    }

    return 0;
}
```

**Notes**

- `%f` prints a `double` in `printf`, but float arguments are promoted to `double`, so `printf("%.2f", some_float)` is fine.
- Pointer arithmetic with `float *` steps in units of `sizeof(float)`. This is why `q+1` points to the next float in the array. We can see here how closely linked pointers and arrays are in C.

---

### Example 2 — Basics (int)

```c
// pointers_basics.c
#include <stdio.h>

int main(void)
{
    // initialise a variable with a value
    int variable = 255;

    // create a pointer to the variable's address
    int *pointer = &variable;

    // print the value and the address
    printf("Variable = %d\n", variable);
    printf("Variable address = %p\n", pointer);

    // use the pointer to read the value (dereference)
    printf("Variable value via pointer = %d\n", *pointer);

    return 0;
}
```

**Notes**

- `&variable` gives the address of `variable`.
- `*pointer` reads (or writes) the object that `pointer` points to.
- Use `%p` to print addresses - these will change each time you run the program.

**Typical output (addresses differ per run):**
```
Variable = 255
Variable address = 0x7ffd52c4d8c4
Variable value via pointer = 255
```

> Avoid creating an uninitialised pointer (often called a *wild* or *dangling* pointer). Always initialise pointers either to a valid address or to `NULL`. Leaving a pointer uninitialised and then dereferencing it leads to undefined behaviour - we don't know what memory it points to!

### Example 3 — Repointing a pointer and using NULL

```c
// pointers_repoint.c
#include <stdio.h>
#include <stddef.h>   // for NULL

int main(void)
{
    double a = 0.0, b = 0.0;

    printf("a = %.1f, b = %.1f\n", a, b);
    printf("&a = %p, &b = %p\n", &a, &b);

    // create a 'null' pointer in C
    double *pointer = NULL;
    printf("Pointer initialised to: %p\n", pointer);

    // point to 'a' and assign via the pointer
    pointer = &a;
    printf("Pointer now pointing to 'a': %p\n", pointer);
    *pointer = 99.9;

    // now point to 'b' and assign via the pointer
    pointer = &b;
    printf("Pointer now pointing to 'b': %p\n", pointer);
    *pointer = 77.7;

    printf("a = %.1f, b = %.1f\n", a, b);
    return 0;
}
```

---

## Endianness

Multi-byte objects (e.g. a 32-bit `uint32_t`) occupy several bytes in memory. *Endianness* is the order in which those bytes are laid out:

- **Little-endian:** least significant byte at the lowest address.
- **Big-endian:** most significant byte at the lowest address.

C allows you to examine an object’s representation by reading it via an `unsigned char *` (byte view). The example below prints each byte of a 32-bit value in memory order.

```c
// endianness_demo.c
#include <stdio.h>
#include <stdint.h>   // for uint32_t
#include <inttypes.h> // for PRIX32

int main(void)
{
    uint32_t value = 0xABCDEF89u;

    printf("Value = 0x%08" PRIX32 "\n", value);
    printf("Size of value = %zu bytes\n", sizeof value);
    printf("Address of value = %p\n", (void *)&value);

    // view the same memory as bytes
    unsigned char *p = (unsigned char *)&value;

    puts("-----------------------");
    for (size_t i = 0; i < sizeof value; ++i) {
        printf("%p  0x%02X\n", (void *)(p + i), p[i]);
    }
    puts("-----------------------");

    return 0;
}
```

Sample output:

```
Value = 0xABCDEF89
Size of value = 4 bytes
Address of value = 0x7ffd43688a6c
-----------------------
0x7ffd43688a6c  0x89
0x7ffd43688a6d  0xEF
0x7ffd43688a6e  0xCD
0x7ffd43688a6f  0xAB
-----------------------

**Interpretation**

If you see the first printed byte as `0x89` at the lowest address, your system is little-endian. If it begins with `0xAB`, it’s big-endian.

---


## Alignment and types

- Many targets require certain types to be naturally aligned (e.g. 4-byte alignment for `int` on many 32-bit systems). This is why `sizeof` and addresses often line up neatly.
- A pointer’s *type* matters. An `int *` points to `int`; pointer arithmetic advances in units of that type (e.g. `p + 1` moves by `sizeof *p` bytes).
- You may safely inspect any object’s bytes through an `unsigned char *`. Avoid other type-punning tricks as they can invoke undefined behaviour.

## Common pitfalls

- **Uninitialised pointers.** Always set pointers to a valid address or `NULL` before use.
- **Wrong `printf` formats.** Use `%p` for addresses and cast to `(void *)`. Match integer formats to their types.
- **Forgetting `&`.** To pass the address of a variable (e.g. to `scanf`), remember `&variable` for non-array scalars.
- **Dangling pointers.** Don’t use a pointer after the object it points to has gone out of scope or been freed.
- **Mismatched types.** Don’t assign between incompatible pointer types without a cast—and even with a cast, ensure it’s safe.
