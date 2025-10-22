---
title: Prototypes
parent: Functions
nav_order: 1
layout: default
---

# Functions in C

A **function** is a named block of code that:

- can **take inputs** (parameters),
- **does some work**, and
- optionally **returns a result**.

Think of it as a **reusable tool** you can call whenever you need that job done.

```c
int add(int a, int b) {     // function definition
    return a + b;
}

int main(void) {
    int sum = add(2, 3);    // function call
    // sum == 5
}
```

### Why use functions?

- **Readability**: Breaks a big problem into clear steps with names.
- **Reusability**: Write it once, use it many times.
- **Testing**: Easy to test pieces in isolation.
- **Abstraction**: Hide low‑level details behind a clean interface (great for embedded drivers).

---

## What is a function *prototype*?

A **function prototype** is a **declaration** that tells the compiler—*before the function is used*—the function’s:

- **name**
- **return type**
- **parameter types (and order)**

It’s like a **contract**: *“You can call this function now. The rest of it will come later.”*

```c
// === Prototype (declaration) ===
int add(int a, int b);

int main(void) {
    int s = add(10, 20); // OK: the compiler already knows the signature
    // ...
}

// === Definition (the body) ===
int add(int a, int b) {
    return a + b;
}
```

---

## Why use prototypes at all?

- **Guarantee correct calls**: If functions call each other, prototypes ensure the compiler knows their signatures before any call happens. This prevents mismatched arguments or return types.
- **Separate compilation**: In multi‑file projects, you often compile `.c` files separately. Prototypes in headers (`.h`) make it clear what functions belong to a library and allow other files to call them without seeing the full definition.
- **Type safety**: The compiler can check argument types and return types early, avoiding subtle bugs.

---

## Why put prototypes at the top?

C is compiled **top-to‑bottom**. When the compiler sees a call like `add(10, 20);`, it must already know:

- what `add` returns (so it can type‑check and generate correct calling code), and
- what types its parameters are (so it can check your arguments).

Without a prior declaration *or* a definition above the call, modern C (C99 and later) treats this as a **compile error** (or at least a strong diagnostic). Prototypes **solve this** by declaring the function **before** it’s called.

> **Rule of thumb**  
> Either **define** the function *above* its first use, **or** put a **prototype** at the top (or in a header that you `#include`).

---

## Minimal examples

### 1) Calling a function *before* it’s defined → needs a prototype

```c
#include <stdio.h>

int add(int a, int b);   // <-- prototype at the top

int main(void) {
    printf("%d\n", add(2, 3)); // OK
}

int add(int a, int b) {  // definition later
    return a + b;
}
```

### 2) If you define first, you can skip the prototype (for single file)

```c
#include <stdio.h>

int add(int a, int b) {     // definition appears before any call
    return a + b;
}

int main(void) {
    printf("%d\n", add(2, 3)); // OK
}
```

### 3) Multi‑file projects: put prototypes in a header

`math_utils.h`
```c
#ifndef MATH_UTILS_H
#define MATH_UTILS_H

int add(int a, int b);     // prototypes live in headers
int mul(int a, int b);

#endif
```

`math_utils.c`
```c
#include "math_utils.h"

int add(int a, int b) { return a + b; }
int mul(int a, int b) { return a * b; }
```

`main.c`
```c
#include <stdio.h>
#include "math_utils.h"   // bring in the prototypes

int main(void) {
    printf("%d\n", add(2, 3));
    printf("%d\n", mul(4, 5));
}
```

Compile:
```bash
gcc -Wall -Wextra -Wpedantic main.c math_utils.c -o app
```

---

## Summary

- Use **functions** to organise, reuse, and test code.  
- The compiler must know a function’s **signature before the first call**.  
- Put **prototypes at the top** or in a **header** included by any file that calls them.  
- Keep **definitions** in `.c` files; **declarations (prototypes)** in `.h` files for sharing.  

---
