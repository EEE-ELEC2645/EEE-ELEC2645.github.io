---
title: Typedef
parent: C Basics
nav_order: 12
layout: default
---

# Typedef

`typedef` lets you create a **shorthand alias** for an existing type. This is particularly useful for making your code more readable and reducing repetitive typing – especially when working with structs, enums, or complex types.

## Basic Syntax

```c
typedef existing_type new_name;
```

For example, instead of writing `unsigned long int` everywhere, you can create a shorter alias:

```c
typedef unsigned long int ulong;

ulong my_variable = 100;  // Much easier to read!
```

---

## Typedef with Structs

Without `typedef`, you have to write `struct` every time you use your struct:

```c
struct Point2D {
  int x;
  int y;
};

struct Point2D origin = { 0, 0 };  // Note the 'struct' keyword
struct Point2D dest = { 10, 20 };
```

With `typedef`, you can skip the `struct` keyword:

```c
typedef struct {
  int x;
  int y;
} Point2D;

Point2D origin = { 0, 0 };  // Much cleaner!
Point2D dest = { 10, 20 };
```

---

## Typedef with Enums

Similarly, `typedef` makes enums cleaner:

```c
// Without typedef
enum Direction { UP, DOWN, LEFT, RIGHT };
enum Direction player_dir = UP;

// With typedef
typedef enum { UP, DOWN, LEFT, RIGHT } Direction;
Direction player_dir = UP;  // Cleaner!
```

---

## Common Uses

`typedef` is commonly used for:
- **Structs and enums** – reduces repetition
- **Function pointers** – makes complex function pointer syntax more readable
- **Platform-specific types** – e.g., `uint8_t`, `size_t` are often typedefs

---

## Tips

- Use `typedef` to make your code more readable, not to obscure what's happening
- Don't overuse it – sometimes writing `struct MyStruct` makes it clearer that you're dealing with a struct
- Convention: Many programmers suffix typedef'd struct names with `_t` (e.g., `point_t`) to indicate it's a typedef. We do this in the Unit 3 & 4 libraries, but this isn't required

