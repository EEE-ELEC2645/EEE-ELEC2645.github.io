---
title: Software Projects
parent: C Projects
nav_order: 3
layout: default
---

# Working with Multiple Files in C

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

> When programs get bigger, keeping everything in **one** `.c` file gets messy. Splitting code into **logical modules** (each with a `.h` header and a `.c` source) makes it easier to read, test, and reuse.

---

## Introduction

For tiny programs, a single file with `main()` and a few helper functions is fine. For anything larger, split the project into **blocks of functionality** (modules). Each block usually has:

- A **header** (`.h`) with *declarations* (function prototypes, constants, `typedef`s).
- A **source** (`.c`) with the *definitions* (the actual code).

`main.c` becomes the “glue” that includes the headers and calls functions across modules. This modularisation is also how we build **libraries** you can reuse across projects: the header defines the **interface** or API (Application Programming Interface) and the source implements it.

---

## Headers and Interfaces

- **Header (`.h`)**: what a module *offers* (function prototypes and public constants).  
- **Source (`.c`)**: how that module *works* (function bodies).  
- Use `#include "mylib.h"` for **your** headers, and `#include <stdio.h>` for **system/standard** headers. The angle brackets `<>` tell the compiler to search system include paths first; quotes search the current directory before system paths.

> Think of the header as the **contract**. If `main.c` includes the header, the compiler knows the function names, parameter/return types, and can type‑check calls, even though the code lives in another file.

---

## Example (C version): A Tiny Maths “Library”

Lets make a minimal library with a single function `sum(int, int)` and call it from `main.c`. The header advertises the function; the source implements it.

### `main.h`
```c
#ifndef MAIN_H
#define MAIN_H

/* Pull in the maths module’s interface */
#include "maths.h"

#endif /* MAIN_H */
```

### `maths.h`
```c
#ifndef MATHS_H
#define MATHS_H

/* Function takes two integers and returns their sum */
int sum(int a, int b);

#endif /* MATHS_H */
```

### `maths.c`
```c
#include "maths.h"

int sum(int a, int b) {
    return a + b;
}
```

### `main.c`

```c
#include <stdio.h>
#include "main.h"   /* brings in maths.h via main.h */

int main(void) {
    int total = sum(5, 6);
    printf("Sum = %d\n", total);
    return 0;
}
```

**Compile (small projects):**

```bash
gcc -std=c11 -Wall -Wextra -Werror main.c maths.c -o main.out
```

You don’t list headers on the command line, the `.c` files include them. If your headers are in the current directory and the compiler can’t find them, add `-I.` to the compile command to tell gcc to search “.” for headers.

---

## Makefiles (Why and How)

Typing long compile commands gets old fast! A **Makefile** saves the build steps into named *targets* so you can just run `make`. The default file name is `Makefile`; if you use another name, run `make -f MyMakefile`. We have been using these in the Unit 1 Activities

### Small, single‑rule Makefile

```make
# Makefile
main.exe: main.c maths.c
	@gcc -std=c11 -Wall -Wextra -Werror main.c maths.c -o main.out
```

- First line: **target** (`main.exe`) and its **dependencies** (`main.c maths.c`).  
- Second line (**tab**, not spaces!) is the build command. If nothing changed, `make` will say the target is up to date and do nothing.

### Faster rebuilds with object files

For larger projects, compile each source to an object (`.o`) and **link** them. Then, only the **changed** `.c` files rebuild. `make` can also use variables so you don't have to keep repeating the compiler and flags and filenames. 

```make
# Makefile (object-based)
CC      = gcc
CFLAGS  = -std=c11 -Wall -Wextra -Werror -c
LDFLAGS =
OBJS    = main.o maths.o

all: main.exe

main.exe: $(OBJS)
	$(CC) $(OBJS) $(LDFLAGS) -o $@

main.o: main.c main.h maths.h
	$(CC) $(CFLAGS) main.c

maths.o: maths.c maths.h
	$(CC) $(CFLAGS) maths.c

clean:
	@rm -f *.o main.exe
```

- First build: compile all `.c` → `.o`, then link.  
- Subsequent builds: `make` recompiles only what changed, then links.  
- `make clean` removes build artefacts before sharing or zipping your project.

---

## Common Pitfalls (and Fixes)

1. **Putting definitions in headers**  
    Don’t put *function bodies* or *non‑`extern` global variables* in `.h` files; you’ll get duplicate definition errors when multiple `.c` files include the header. Keep headers to **declarations**; put code and single definitions in **one** `.c`.

2. **Including `.c` files**  
    Never do `#include "module.c"` from another `.c`. Include **headers**; compile each `.c` and **link** them. Otherwise you’ll pull the same definitions in twice and the linker will complain about duplicates.

3. **Forgetting include guards**  
    Always guard headers with `#ifndef/#define/#endif` to prevent multiple inclusion.

---

## The `extern` Keyword (Sharing a Global Across Files)

Global variables are often discouraged, but they do appear in embedded C (e.g., a status flag or a hardware handle). If multiple files need to *use the same* global, do it like this: put an **`extern` declaration** in the header, and a **single definition** in **one** `.c` file. This fixes both “not declared” and “duplicate symbol” errors seen when naively declaring a global in a header.

### `main.h`

```c
#ifndef MAIN_H
#define MAIN_H

/* This declares that a variable exists somewhere (no storage here). */
extern int global_variable;

/* Function in another module we'll call */
void change_global_variable(void);

#endif /* MAIN_H */
```

### `extern_demo.c`  *(the other module)*

```c

#include "main.h"

void change_global_variable(void) {
    /* Use the variable declared 'extern' in main.h
       and defined once in main.c */
    global_variable = 0;
}
```

### `main.c`

```c
#include <stdio.h>
#include "main.h"

/* Exactly one definition (with storage) in the whole program */
int global_variable = 99;

int main(void) {
    change_global_variable();
    printf("Global variable = %d\n", global_variable); /* prints 0 */
    return 0;
}
```

**Build:**
```bash
gcc -std=c11 -Wall -Wextra -Werror -c main.c
gcc -std=c11 -Wall -Wextra -Werror -c extern_demo.c
gcc main.o extern_demo.o -o extern_demo
```

> Why not put `int global_variable = 99;` in a header? Because every `.c` that includes it would get its **own** definition so the the linker sees **duplicate symbols**. `extern` in the header declares the name then one `.c` file provides the single definition.

---

## Summary

- Split growing programs into modules: **`.h` for interface**, **`.c` for implementation**.  
- Include **headers** (not `.c` files) and compile each `.c`, then **link**.  
- Use **Makefiles** so `make` rebuilds only what changed.  
- Share globals across files with **`extern` in headers** and **one real definition** in a `.c`.  
These practices keep projects organised and scale from student labs to real embedded products.

---

### Quick Compile Cheat‑Sheet

```bash
# One-shot build (small demos)
gcc -std=c11 -Wall -Wextra -Werror main.c maths.c -o main.exe

# Separate compilation (good habit)
gcc -std=c11 -Wall -Wextra -Werror -c main.c
gcc -std=c11 -Wall -Wextra -Werror -c maths.c
gcc main.o maths.o -o main.exe
```
(Use `-I.` if headers live in the current directory and gcc can’t find them.)
