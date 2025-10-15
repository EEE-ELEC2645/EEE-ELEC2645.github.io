---
title: Enums
parent: C Basics
nav_order: 9
layout: default
---

# Enums

## Introduction

An enum, or *enumerated type*, is a data type that consists of a list of **named integer constants**.
By default, the first value is `0`, the second is `1`, and so on (unless you assign explicit values).
Enums make code more readable than raw integers.

For instance, you might want to represent different directions on a gamepad. You *could* use a bunch of integers `(0 = up, 1 = down , …)`, and just remember which number corresponds to which. But that’s hard to read and easy to forget! An `enum` makes the intent clear:

```c
// by default starts at 0 and increments by 1
enum direction {up, down, left, right}; 

// Enum starting at 1, each subsequent value increments by 1
enum pokemon {Bulbasaur =1, Ivysaur , Venusaur, Charmander  , Charmeleon , Charizard };

// Enum with specific values
enum ansi_color {
  ANSI_BLACK  = 40,
  ANSI_WHITE  = 47,
  ANSI_BLUE   = 44,
  ANSI_GREEN  = 42,
  ANSI_RED    = 41
};
```

These are also handy when you want to ensure that only certain values are possible. If you were storing "Direction" as an `int` from 1 to 4 you might accidentally set it to a different which could cause unexpected behaviour later. Using an enum prevents this mistake from happening with a compiler error:

```c
#include <stdio.h>

enum DIR {UP=0, DOWN=1, LEFT=2, RIGHT=3}; 
int main(void) {
  enum DIR mydir = UP;

  mydir = DOWN; // This is fine
  mydir = SIDEWAYS; // This will give a compiler error as SIDEWAYS is not part of the enum
  mydir = 5; // This is allowed as enums are really just integers in the background, but is bad practice to assign enums like this

  printf("Direction is %d\n", mydir);
 
return 0;
}

```

## Problems with comparing Enums

If you had two `enums` for different things, say fruits and vegetables, you might expect that comparing two variables of different enum types might give you an error. However, as `enums` are really integers in the background comparisons are allowed and can get confusing:

```c
#include <stdio.h>

// Constants in the list are assigned integers: 0, 1, 2 …
enum Fruit { Apple, Banana, Pear };
enum Vegetable { Potato, Carrot, Onion };

int main(void) {
  enum Fruit myfruit = Apple;          // 0
  enum Vegetable myveg = Potato;       // 0

  // In C, different enum types are still just integers under the hood.
  // This comparison is allowed and will be true here because both are 0.
  if (myfruit == myveg) {
    printf("The types are the same!\n");   // prints (numeric equality, not semantic)
  }
  return 0;
}
```

This improves readability (you write `Apple` instead of `0`), but exposes this classic pitfall: because `C` enums are essentially integers, **comparing values from different enums can succeed by accident**.

---

## Avoiding problems with Enums

To limit the chances of errors when accidentally comparing two different `enums` there are a couple of approaches we can take:

- 1  **Prefix enumerators** so their domain is obvious, i.e. `FRUIT_APPLE` makes it clear it is part of an `enum` called fruit

```c
#include <stdio.h>

enum fruit_t { FRUIT_APPLE, FRUIT_BANANA, FRUIT_PEAR } ;
enum vegetable_t { VEG_POTATO,  VEG_CARROT,  VEG_ONION   } ;

int main(void) {
  enum fruit_t fItem = FRUIT_APPLE;
  enum vegetable_t vItem = VEG_POTATO;

  // if (fItem == vItem) { ... } // Still legal in C, but prefixing makes the mistake obvious.
  (void)fItem; (void)vItem; // suppress compiler warnings that the variables haven't been used
  return 0;
}
```

- 2 **Wrap the enum in a struct** to create truly distinct types that won’t compare directly. This also prevents "direct assignment" i.e. we cannot do something like `myEnum =5`. We will look at structs and `typedef` more closely later in the course. This approach is more complicated, but worth it for safety reasons if you think it might happen in your code:

```c
#include <stdio.h>

// typedef is just a way to create a new type name for an existing type, in this case an enum
typedef enum { FRUIT_APPLE, FRUIT_BANANA, FRUIT_PEAR } fruit_e;
typedef enum { VEG_POTATO,  VEG_CARROT,  VEG_ONION   } vegetable_e;

typedef struct { fruit_e value; } fruit_t;
typedef struct { vegetable_e value; } vegetable_t;

int main(void) {
  fruit_t f = { .value = FRUIT_APPLE };
  vegetable_t v = { .value = VEG_POTATO };
  // if (f == v) { }   // compile error (different types)

  if (f.value == FRUIT_APPLE) { // == VEG_POTATO would give compiler error
    printf("apple\n");
  }
  if (v.value == VEG_POTATO) {
    printf("potato\n");
  }
  return 0;
}
```

---

## Summary

- **Enums** replace magic numbers with names, improving readability. Remember: different enums can still return equal when comparing in `C` because they’re integers - write code carefully to avoid these comparisons or use a wrapper.
