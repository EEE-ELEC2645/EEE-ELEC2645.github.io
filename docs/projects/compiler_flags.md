---
title: Compiler Flags
parent: C Projects
nav_order: 1
layout: default
---



---

# What is `gcc`?

`gcc` stands for **GNU Compiler Collection**. For C programs, it’s the tool that:

- **Translates your C source code** (`.c` files) into **machine instructions** your CPU can execute.
- Performs **compilation** (turns code into object files) and **linking** (combines objects into an executable).
- Supports many languages (C, C++, etc.), but in our examples we use it for **C only**.

Think of `gcc` as the bridge between **human-readable C code** and **binary instructions** that run on hardware.

---

# Common `gcc` Compiler Flags (for C)

When building multi‑file projects, compiler flags help enforce good habits and catch bugs early. Here are the most useful ones for us in Semester 1 and for embedded projects in Semester 2:

## **`-lm`**

- Links your program against the math library (`libm`), which provides functions from <math.h> and <complex.h> like `sin`, `cos`, `sqrt`, `pow`, `exp`, `log`, `erf`, `hypot`, `isnan`, `isfinite` etc.
- Why? On many Unix-like systems (such as the Codepsaces) the math functions live in a separate library (`libm`) that the linker in gcc doesn’t include automatically. If you call any of these functions without -lm, you’ll typically get `“undefined reference”` linker errors.

---

## **`-Wall`**

- Enables **all common warnings**.
- Why? Warnings often point to real bugs (e.g., unused variables, missing return).

---

## **`-Wextra`**

- Adds **extra warnings** beyond `-Wall`.
- Why? C is permissive; extra warnings catch subtle mistakes like unused parameters.

---

## **`-Werror`**

- Treats **warnings as errors**.
- Why? Forces you to fix issues immediately instead of ignoring them. Can be annoying as sometimes your code can have warnings but still work, but good practice to solve them anyway.

---

## **`-o <file>`**

- Sets the **output file name**.
- Example: `-o main.exe` creates executable named `main.exe` instead of `a.out`.

---

## **`-std=c11`** (Optional)

- Tells the compiler to use the **C11 standard** which is modern, and widely supported. Well, *relatively* modern. Embedded systems are often written in **C99 standard** for extreme backwards capability.
- Why? Ensures consistent behaviour, and newer versions have more features. Its not always clear which version of C is used by default, so often a good idea to say so explicitly.

# Quick Example For A Single File

```bash

gcc -std=c11 -Wall -Wextra -Werror main.c -o main.out
```

**What these Flags Do**
- `-std=c11` → Use modern C standard. (optional but good practice)
- `-Wall -Wextra` → Show all common and extra warnings.
- `-Werror` → Treat warnings as errors (forces fixes).
- `main.c` → Compile source file.
- `-o main.out` → Name the output executable `main.out`. The Codespaces are running Linux, so we can actually use any file extension we like but on windows this would need to be `main.exe`

# Multiple Files

You can include more `.c` files in the compile command if you have code split across multiple files:

### Quick Compile Command (Multi‑File Project)

```bash
gcc -std=c11 -Wall -Wextra -Werror main.c maths.c -o main.exe
```

 **Multi‑file builds in practice:**  
 You *can* compile and link everything in one shot as above (e.g., `gcc main.c maths.c -o main.exe`), but that **recompiles all files every time**. A better pattern is a **two‑step build**:
 

 1. Compile each source to an object (`gcc -c main.c` and `gcc -c maths.c`) which are `.o` files. These are machine code but not fully executable yet. 
 2. Then **link** the objects (`gcc main.o maths.o -o main.exe`) to create the final executable program. 
 

 This enables **incremental builds** where only the changed `.c` files are recompiled while unchanged `.o` files are reused (and tools like `make` automate this). *(Note: `gcc -c main.c maths.c` will create both `.o` files in one command, but having separate lines are what let build systems skip unnecessary work.)*

So for **separate compilation** (better for larger projects):

```bash
gcc -std=c11 -Wall -Wextra -Werror -c main.c
gcc -std=c11 -Wall -Wextra -Werror -c maths.c
gcc main.o maths.o -o main.exe
```

To summarise the useful commands here:

## **`-c`**

- Compile to **object code** (`.o`) without linking.
- Why? Used in multi‑file builds and Makefiles for faster incremental compilation.

---

## **`-I.`**

- Adds the **current directory** to the header search path.
- Why? Needed when your headers are in the same folder as your sources.

---

Later we will look at `make` and other ways of creating bigger projects that save all this typing! 