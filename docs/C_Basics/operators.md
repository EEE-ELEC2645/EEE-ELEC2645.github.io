---
title: Operators
parent: C Basics
nav_order: 2
layout: default
---

# Common operators in C

Operators tell C to perform an operation on one or more values. You will use some of these, such as `+` and `=`, in almost every program.

This page covers the operators we will use most often. For a full reference, see: [GeeksforGeeks: Operators in C](https://www.geeksforgeeks.org/c/operators-in-c/). We will look at bitwise operators separately, as they need a bit more explanation.

## Arithmetic operators

The arithmetic operators work much as you would expect:

```c
int a = 10;
int b = 3;

int sum = a + b;          // 13
int difference = a - b;   // 7
int product = a * b;      // 30
int quotient = a / b;     // 3
int remainder = a % b;    // 1
```

The `%` operator gives the remainder after integer division. It is often called the **modulo** operator.

For example:

```c
int remainder = 10 % 3;   // 1
```

One common use of `%` is checking whether a number is even:

```c
if (value % 2 == 0) {
    printf("value is even\n");
}
```

### Integer division

When both operands are integers, C performs integer division:

```c
int result = 10 / 3;      // 3
```

The result is `3`, not `3.333`. The fractional part is discarded.

If you want a floating-point result, at least one operand must be a floating-point value:

```c
float result = 10.0f / 3.0f;
```

Be careful with the following:

```c
float result = 10 / 3;
```

The division happens before the result is stored. Since `10` and `3` are both integers, the division produces `3`, which is then converted to `3.0f`.

## Relational operators

Relational operators compare two values:

| Operator | Meaning |
|:---------|:--------|
| `==` | Equal to |
| `!=` | Not equal to |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal to |
| `<=` | Less than or equal to |

For example:

```c
int temperature = 24;

bool is_hot = temperature > 30;
bool is_freezing = temperature <= 0;
bool is_room_temperature = temperature == 24;
```

Remember that `==` compares two values, while `=` assigns a value:

```c
x = 5;          // Assign 5 to x
x == 5;         // Check whether x is equal to 5
```

Mixing these up is a very common mistake:

```c
if (x = 5) {
    // This assigns 5 to x. It does not compare x with 5.
}
```

Compiler warnings will normally help you catch this, so pay attention to them!

## Relational operators

Relational operators compare two values:

| Operator | Meaning |
|:---------|:--------|
| `==` | Equal to |
| `!=` | Not equal to |
| `>` | Greater than |
| `<` | Less than |
| `>=` | Greater than or equal to |
| `<=` | Less than or equal to |

To use the `bool`, `true` and `false` types in C, include `<stdbool.h>`:

```c
#include <stdbool.h>

int temperature = 24;

bool is_hot = temperature > 30;
bool is_freezing = temperature <= 0;
bool is_room_temperature = temperature == 24;
```

Remember that `==` compares two values, while `=` assigns a value:

```c
x = 5;          // Assign 5 to x
x == 5;         // Check whether x is equal to 5
```

Mixing these up is a very common mistake:

```c
if (x = 5) {
    // This assigns 5 to x. It does not compare x with 5.
}
```

Compiler warnings will normally help you catch this, so pay attention to them!

## Logical operators

Logical operators combine or invert conditions:

| Operator | Meaning |
|:---------|:--------|
| `&&` | Logical AND |
| `||` | Logical OR |
| `!` | Logical NOT |

For example:

```c
#include <stdbool.h>

int temperature = 25;
bool button_pressed = true;

if (temperature > 20 && button_pressed) {
    printf("Both conditions are true\n");
}

if (temperature > 30 || button_pressed) {
    printf("At least one condition is true\n");
}

if (!button_pressed) {
    printf("The button is not pressed\n");
}
```

An `&&` expression is true only when both conditions are true. An `||` expression is true when either condition is true.

C treats zero as false and any non-zero value as true. This means the following works:

```c
int error = 0;

if (!error) {
    printf("No error\n");
}
```

However, using `bool` for values that represent true or false usually makes the purpose of the variable clearer.

### Short-circuit evaluation

C stops evaluating a logical expression as soon as it knows the result.

For `&&`, if the first condition is false, the second condition is not evaluated:

```c
int index = 12;
int array_length = 10;

if (index >= 0 && index < array_length) {
    printf("The index is valid\n");
}
```

If `index >= 0` is false, C does not need to evaluate `index < array_length`, because the complete `&&` expression cannot be true.

This becomes particularly useful when the second condition should only be checked after the first one succeeds:

```c
int value = 0;

if (value != 0 && 100 / value > 5) {
    printf("The result is greater than 5\n");
}
```

If `value` is zero, the second condition is not evaluated. This avoids attempting to divide by zero.

For `||`, if the first condition is true, the second condition is not evaluated:

```c
int value = 0;

if (value == 0 || 100 / value > 5) {
    printf("At least one condition is true\n");
}
```

Again, if `value` is zero, the division is not performed. This behaviour is called **short-circuit evaluation**.

## Assignment operators

The assignment operator `=` stores a value in a variable:

```c
int x = 5;

x = 10;
```

C also provides compound assignment operators for updating a variable:

| Operator | Example | Equivalent expression |
|:---------|:--------|:----------------------|
| `+=` | `x += 2;` | `x = x + 2;` |
| `-=` | `x -= 1;` | `x = x - 1;` |
| `*=` | `x *= 3;` | `x = x * 3;` |
| `/=` | `x /= 2;` | `x = x / 2;` |
| `%=` | `x %= 3;` | `x = x % 3;` |

For example:

```c
int score = 10;

score += 5;     // score is now 15
score -= 2;     // score is now 13
```

The type of the variable still matters. If `x` is an integer, `x /= 2` uses integer division.

## Increment and decrement

The increment operator `++` adds one to a variable. The decrement operator `--` subtracts one:

```c
int i = 0;

i++;    // i is now 1
i--;    // i is now 0
```

You will see these operators frequently in `for` loops:

```c
for (int i = 0; i < 10; i++) {
    printf("%d\n", i);
}
```

There is a difference between putting the operator before or after the variable:

```c
i++;
++i;
```

Both increase `i` by one. The difference matters when the result is used as part of a larger expression:

```c
int i = 5;

int a = i++;    // a is 5, then i becomes 6
int b = ++i;    // i becomes 7, then b is set to 7
```

This can be difficult to read, so avoid combining increment or decrement with other operations unless there is a good reason.

## Operator precedence

C applies operators in a particular order, in the same way that multiplication is normally performed before addition in mathematics:

```c
int result = 2 + 3 * 4;   // 14
```

Use parentheses when they make the intended order clearer:

```c
int result = (2 + 3) * 4; // 20
```

Even if you know the precedence rules, parentheses can make an expression easier for someone else to read.

