---
title: Header Guards
parent: C Projects
nav_order: 2
layout: default
---

# Header Guards

### Introduction

Consider the following headers and source file

**`Person.h`**

```c
typedef struct {
    char name[32];  /* fixed-size character array for C */
} Person;
```

Here we simply have a C `struct` that stores a person’s name in a fixed-size character array.

**`Parents.h`**

```c
#include "Person.h"

typedef struct {
    Person father;
    Person mother;
} Parents;
```

This `struct` stores a set of parents. Since a parent is a person, we reuse the `Person` type to represent each parent (so we can set their names). We may then use these headers in our `main.c` file.

**`main.c`**

```c
#include "Parents.h"
#include "Person.h"
#include <stdio.h>
#include <string.h>

int main(void) {
    Person best_friend;
    strcpy(best_friend.name, "Alex");
    printf("My best friend is %s.\n", best_friend.name);

    Parents my_parents;
    strcpy(my_parents.father.name, "Miles");
    strcpy(my_parents.mother.name, "Rey");
    printf("My parents are called %s and %s.\n",
           my_parents.father.name, my_parents.mother.name);

    return 0;
}
```

This code creates a `Person` used to hold the details of our best friend and also creates an instance of `Parents` to hold details of our parents. Note that we include the header files we use in `main.c`. We also include `<stdio.h>` for `printf` and `<string.h>` for `strcpy`.

> **Note:** These examples use `strcpy` for simplicity. In reality we would prefer `snprintf` or `strncpy` with explicit bounds checks.

---

### Compilation error

Although everything looks reasonable, this code will **not** compile as written because the same type ends up being defined more than once.

A typical compiler error (messages vary by compiler) might be:

```
In file included from main.c:2:
Person.h:1:1: error: redefinition of 'Person'
 typedef struct {
 ^~~~~~~
In file included from Parents.h:1,
                 from main.c:1:
Person.h:1:1: note: previous definition is here
 typedef struct {
 ^~~~~~~
```

Looking at the error message, it suggests that our `Person` type has been defined more than once. How has this happened?

---

### `#include` pre-processor directive

The `#include` pre-processor directive essentially copies the contents of the included file into the current translation unit. After preprocessing, our `main.c` will conceptually look something like this (comments added):

```c
/* From Person.h (pulled in via Parents.h first) */
typedef struct {
    char name[32];
} Person;

/* From Parents.h */
typedef struct {
    Person father;
    Person mother;
} Parents;

/* From Person.h again (direct include in main.c) */
typedef struct {
    char name[32];
} Person;  /* <-- Redefinition occurs here */

#include <stdio.h>
#include <string.h>

int main(void) {
    Person best_friend;
    strcpy(best_friend.name, "Alex");
    printf("My best friend is %s.\n", best_friend.name);

    Parents my_parents;
    strcpy(my_parents.father.name, "Miles");
    strcpy(my_parents.mother.name, "Rey");
    printf("My parents are called %s and %s.\n",
           my_parents.father.name, my_parents.mother.name);
    return 0;
}
```

Since both `main.c` and `Parents.h` include `Person.h`, the definition of `Person` now appears **twice**. This duplication causes the compilation error. So how do we fix it?

---

### Header guards

We use **header guards** in header files to prevent them being included multiple times.

**`Person.h` (with header guards)**

```c
#ifndef PERSON_H
#define PERSON_H

typedef struct {
    char name[32];
} Person;

#endif /* PERSON_H */
```

**`Parents.h` (with header guards)**

```c
#ifndef PARENTS_H
#define PARENTS_H

#include "Person.h"

typedef struct {
    Person father;
    Person mother;
} Parents;

#endif /* PARENTS_H */
```

The `#ifndef` directive checks to see if a token has **not** already been defined in the code. If it has not, then we `#define` it and include the code between `#ifndef` and `#endif`. If it has already been defined (i.e., the header has already been included elsewhere), then that block is skipped and the code will not be included again. This prevents the header from appearing more than once.

A common naming convention for the guard macro is the uppercase file name with underscores (e.g., `PERSON_H`). Many developers also add a helpful comment after `#endif` to show which header is being closed.

---

### Summary

After adding header guards to our header files, the code will compile successfully. When the program runs, we will see the following output in the terminal:

```bash
My best friend is Alex.
My parents are called Miles and Rey.
```

To summarise the summary, **always use header guards!**
