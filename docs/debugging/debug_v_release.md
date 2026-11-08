---
title: Debug vs Release
parent: Debugging
nav_order: 3
layout: default
---

# Debug vs. Release in C


# Debug vs release builds

While developing a program, we often want as much information as possible when something goes wrong.

Once the program is finished, we usually care more about performance and executable size.

For this reason, software is often built in either **debug** or **release** mode.

## Debug builds

A debug build prioritises finding and fixing problems.

Typical features include:

- debug information for a debugger
- additional checks
- debug messages
- little or no optimisation

A typical compiler command might be:

```bash
gcc -g -O0 main.c -o main
```

Here:

```text
-g
```

adds debugging information, while:

```text
-O0
```

disables compiler optimisations.

This makes debugging easier because the generated machine code stays close to the original source code.

## Release builds

A release build prioritises performance and executable size.

Typical features include:

- compiler optimisations enabled
- debug information removed
- debug messages disabled

For example:

```bash
gcc -O2 main.c -o main
```

The exact optimisation level depends on the project, but release builds generally produce smaller and faster executables.

## Enabling debug code

Sometimes it is useful to include code that only runs in a debug build:

```c
#ifdef DEBUG
printf("temperature = %.1f\n", temperature);
#endif
```

This code only exists when `DEBUG` has been defined.

For example:

```bash
gcc -DDEBUG main.c -o main
```

The `-D` option defines a preprocessor symbol.

In this case:

```text
DEBUG
```

is defined, so the `printf()` statement is compiled into the program.

Without:

```bash
-DDEBUG
```

the debug print is removed completely.

## Example

```c
#include <stdio.h>

int main(void)
{
    int value = 42;

#ifdef DEBUG
    printf("Debug: value = %d\n", value);
#endif

    printf("Program running\n");

    return 0;
}
```

Debug build:

```bash
gcc -DDEBUG main.c -o main
```

Output:

```text
Debug: value = 42
Program running
```

Release build:

```bash
gcc main.c -o main
```

Output:

```text
Program running
```
## Debug and release targets in a Makefile

In practice, we normally do not type the debug and release compiler options manually.

Instead, a Makefile often provides separate targets:

```make
debug:
	gcc -g -O0 -DDEBUG main.c -o main

release:
	gcc -O2 main.c -o main
```

We can then build the debug version using:

```bash
make debug
```

or the release version using:

```bash
make release
```

This keeps the build settings in one place and avoids repeatedly typing long compiler commands.

Larger projects often have additional targets such as:

```bash
make test
make debug
make release
make clean
```

The exact names vary between projects, but the idea is always the same: different targets build the project with different settings.

## A useful rule

While developing:

```text
Build for debugging.
```

When releasing software:

```text
Build for performance.
```

The exact settings vary between projects, but the idea is always the same: debug builds help find problems, while release builds focus on the final program.