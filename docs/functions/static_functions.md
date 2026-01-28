---
title: Static Functions
parent: Functions
nav_order: 3
layout: default
---

# Static Functions

The `static` keyword limits a function's **scope** to the file it's defined in. This prevents it from being visible to other compilation units (other `.c` files).

### Why use `static`?

- **Encapsulation**: Keep helper functions private to a single file.
- **Avoid name clashes**: Multiple files can have their own `static` functions with the same name without conflicts.
- **Cleaner API**: Only expose functions that are meant to be used externally.

---

## Example: Private Helper Function

```c
#include <stdio.h>

// This helper is only visible in this file
static int double_value(int x) {
    return x * 2;
}

int calculate_something(int n) {
    return double_value(n) + 10;
}

int main(void) {
    printf("Result: %d\n", calculate_something(5));
    // Output: Result: 20
}
```

Here, `double_value()` is a **static** helper that can't be called from other files. Only `calculate_something()` is externally visible (if declared in a header).

---

## When to use `static`

- **Internal utilities**: Functions like `parse_input()`, `validate_data()`, or `init_hardware()` that are only needed within one file.
- **Multi-file projects**: Prevents accidental use of internal functions from other modules.
- **Team collaboration**: Makes it clear which functions are part of the public interface and which are implementation details.

---

Using `static` is good practice for keeping your code modular and avoiding surprises in larger projects!