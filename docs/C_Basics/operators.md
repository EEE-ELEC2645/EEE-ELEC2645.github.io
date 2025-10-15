---
title: Operators
parent: C Basics
nav_order: 2
layout: default
---

# Common Operators in C

This guide covers the most frequently used operators in C, especially relevant for embedded systems programming. For a full reference, see: [GeeksforGeeks: Operators in C](https://www.geeksforgeeks.org/c/operators-in-c/)

---

### 1. Arithmetic Operators

Used for basic math operations.

```c
int a = 10, b = 3;

int sum     = a + b;
int diff    = a - b;
int product = a * b;
int quotient= a / b;
int mod     = a % b;
```

> Integer division truncates — `10 / 3` gives `3`, not `3.33`. Need to use `double` or `float` to avoid this!

---

### 2. Relational Operators

Used to compare values — returns `true` or `false`. Better to use `bool` to make your intent clear, but we can use `int` too and it will return `1` or `0`

```c
bool equal      = (a == b);
bool not_equal  = (a != b);
bool greater    = (a > b);
bool less       = (a < b);
bool geq        = (a >= b);
bool leq        = (a <= b);
```

---

### 3. Logical Operators

Used to combine or invert boolean expressions, works with `ints` or `bools`

| Operator | Meaning         | Example      | Result (if `a = true`, `b = false`) |
|----------|-----------------|-------------|--------------------------------------|
| `&&`     | Logical AND     | `a && b`    | `false`                             |
| `\|\|`   | Logical OR      | `a \|\| b`  | `true`                              |
| `!`      | Logical NOT     | `!a`        | `false`                             |

Examples:

```c
int a = 1, b = 0;
if (a && b) {
  // This won't run because b is false
}
if (a || b) {
  // This will run because a is true
}
if (!b) {
  // This will run because b is false
}
```

---

### 4. Assignment Operators

Often we want to assign an updated value to a variable like `x = x + 10`. C has some special assignment operators which can be
used to assign and update values, in one go. It saves on typing! 

| Operator | Meaning            | Equivalent Expression |
|----------|--------------------|------------------------|
| `=`      | Assign             | `x = 5`                |
| `+=`     | Add and assign     | `x = x + 2`            |
| `-=`     | Subtract and assign| `x = x - 1`            |
| `*=`     | Multiply and assign| `x = x * 3`            |
| `/=`     | Divide and assign  | `x = x / 2`            |
| `%=`     | Modulo and assign  | `x = x % 3`            |

---

### 5. Increment/Decrement

Used to step values up or down. We use these a lot in `for` loops

```c
int i = 0;

i++;  // i = i + 1
i--;  // i = i - 1

for (int n =0; n < 10 ; n++) {
    // do stuff
}

```

---
