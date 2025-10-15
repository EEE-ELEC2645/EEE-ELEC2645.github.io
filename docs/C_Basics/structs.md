---
title: Structs
parent: C Basics
nav_order: 10
layout: default
---

# Struct

A `struct` lets us create a **custom data structure** by grouping variables of (possibly) different
types into a single unit. This is especially useful for returning multiple, related values from a
function.

Below is a two‑dimensional point. It shows several initialisation and assignment techniques.

```c
#include <stdio.h>

struct Point2D {
  int x;
  int y;
};

int main(void) {
  // Initialise using a brace initialiser list - these are in the order of definition
  // This is the conventional way but its a bit error prone - what happens if you type the order wrong?
  struct Point2D origin = { 0, 0 };

  // Designated initialiser (C99 or later): set fields by name
  // this is the better way of doing it as its less error prone
  struct Point2D destination = (struct Point2D){ .x = 45, .y = 76 };

  // Create an uninitialised struct then assign fields
  struct Point2D corner;
  corner.x = 10;
  corner.y = 5;

  // Reassign using a compound literal (C99 or later)
  corner = (struct Point2D){ 12, 15 };

  printf("origin=(%d,%d) dest=(%d,%d) corner=(%d,%d)\n",
         origin.x, origin.y, destination.x, destination.y, corner.x, corner.y);
  return 0;
}
```

---

## Struct vs. Class

In C++, a `struct` is very similar to a `class` (you can add methods, and the main difference is
default access). **In C, there are no classes or methods.** A C `struct` is just a collection of
fields. If you want your struct to have actions or functionality, you must write separate functions
that operate on your struct.

Using these functions  a C version of the vector‑magnitude idea using helper functions:

```c
#include <stdio.h>
#include <math.h>

struct Vector { int x; int y; };

// takes the vector struct as input to perform an action
double vector_mag(struct Vector v) {
  return sqrt((double)v.x * v.x + (double)v.y * v.y);
}

// takes the vector struct as a pointer so we can edit the variables in the function
void vector_scale(struct Vector *v, int factor) {
    v->x *= factor;
    v->y *= factor;
}


int main(void) {
  struct Vector vec = { 3, 4 };
  double m = vector_mag(vec);
  printf("Magnitude of { %d, %d } = %.2f\n", vec.x, vec.y, m);
  return 0;
}
```

> Conventionally, use **structs** for grouping related data or ["Plain Old Data" (POD)](https://en.wikipedia.org/wiki/Passive_data_structure). Keep behaviour in functions with
> clear names like `vector_mag`, `point_translate`, etc.

---

## Summary

- **Structs** group related fields. In C there are no methods; implement behaviour using functions.
- Prefer **designated initialisers** like `struct Point2D a = (struct Point2D){ .x = 10, .y = 20 };` and keep examples small, clear, and consistent.

---
