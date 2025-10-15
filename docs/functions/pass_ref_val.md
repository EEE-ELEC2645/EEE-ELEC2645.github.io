---
title: Pass by Value vs Pass by Ref
parent: Functions
nav_order: 2
layout: default
---

# Pass-by-Value and Pointers in C

### Introduction

In C, all arguments are passed **by value**. This means the function receives a copy of the variable, not the original. For example:

```c
#include <stdio.h>

void increment(int variable);

int main(void) {
    int variable = 99;
    printf("Variable has a value of: %d\n", variable);
    increment(variable);
    printf("Variable now has a value of: %d\n", variable);
}

void increment(int variable) {
    variable++;
    printf("This variable has a value of: %d\n", variable);
}
```

**Output:**
```
Variable has a value of: 99
This variable has a value of: 100
Variable now has a value of: 99
```

Here, `increment()` modifies its local copy, leaving the original unchanged.

---

### Using Pointers to Modify Caller Variables

To let a function change the variable from the calling function, pass its **address** and use the `*` operator to dereference:

```c
#include <stdio.h>

void increment_ptr(int *variable); // specify we want the pointer *

int main(void) {
    int variable = 99;
    printf("Variable has a value of: %d\n", variable);
    increment_ptr(&variable); // give the address using &
    printf("Variable now has a value of: %d\n", variable);
}

void increment_ptr(int *variable) {
    (*variable)++;
    printf("This variable has a value of: %d\n", *variable);
}
```

**Output:**

```
Variable has a value of: 99
This variable has a value of: 100
Variable now has a value of: 100
```

---

### Multiple “Return” Values

C functions can only return one value directly. To return multiple results, pass pointers to variables:

```c
#include <stdio.h>
#include <math.h>

#define PI 3.14159265359

void circular(double radius, double *area, double *volume);

int main(void) {
    double area, volume;
    circular(1.0, &area, &volume);
    printf("Area = %.5f, Volume = %.5f\n", area, volume);
}

void circular(double radius, double *area, double *volume) {
    *area = PI * pow(radius, 2.0);
    *volume = (4.0 / 3.0) * PI * pow(radius, 3.0);
}
```

**Output:**

```
Area = 3.14159, Volume = 4.18879
```

---

### Key Points

- C **always** passes arguments by value.
- To modify caller data, pass a pointer and dereference it.
- Use `const` for read-only pointers.
- Arrays decay to pointers when passed to functions.

---