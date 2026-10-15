---
title: Makefiles
parent: Building C Projects
nav_order: 5
layout: default
---

# Makefiles

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

Typing the same build commands every time gets annoying very quickly:

```bash
gcc -std=c11 -Wall -Wextra -Wpedantic -c main.c
gcc -std=c11 -Wall -Wextra -Wpedantic -c very_cool_maths.c
gcc main.o very_cool_maths.o -o very_cool_project
```

A Makefile stores these commands so that we can build the project by running:

```bash
make
```

`make` also checks which files have changed and only rebuilds the parts that need updating.

## A simple Makefile

Our project contains:

```text
very_cool_project/
├── main.c
├── very_cool_maths.c
├── very_cool_maths.h
└── Makefile
```

The Makefile is:

```make
CC = gcc
CFLAGS = -std=c11 -Wall -Wextra -Wpedantic

very_cool_project: main.o very_cool_maths.o
	$(CC) main.o very_cool_maths.o -o very_cool_project

main.o: main.c very_cool_maths.h
	$(CC) $(CFLAGS) -c main.c

very_cool_maths.o: very_cool_maths.c very_cool_maths.h
	$(CC) $(CFLAGS) -c very_cool_maths.c

.PHONY: clean

clean:
	rm -f main.o very_cool_maths.o very_cool_project
```

The file must be called `Makefile`, with a capital `M` and no file extension.

## Targets, dependencies and recipes

A Makefile rule has this form:

```make
target: dependencies
	recipe
```

For example:

```make
main.o: main.c very_cool_maths.h
	$(CC) $(CFLAGS) -c main.c
```

Here:

- `main.o` is the target being created
- `main.c` and `very_cool_maths.h` are its dependencies
- the second line is the command used to build it

The command must begin with a tab, not spaces. This is a slightly annoying Makefile requirement!

If either dependency changes, `make` rebuilds `main.o`.

## Variables

The Makefile defines the compiler and its flags near the top:

```make
CC = gcc
CFLAGS = -std=c11 -Wall -Wextra -Wpedantic
```

We use these values with:

```make
$(CC)
$(CFLAGS)
```

This avoids repeating the compiler settings in every rule.

## Building and cleaning

Running:

```bash
make
```

builds the first target in the Makefile. In our example, the first target is:

```make
very_cool_project: main.o very_cool_maths.o
```

Make first checks whether the two object files need rebuilding, then creates the executable.

If only `very_cool_maths.c` has changed, Make recompiles that file and links the program again. It does not recompile `main.c`.

To remove the object files and executable, run:

```bash
make clean
```

The `clean` rule is:

```make
clean:
	rm -f main.o very_cool_maths.o very_cool_project
```

Unlike `very_cool_project`, the `clean` target does not create a file called `clean`. It is just a name for a command we want Make to run.

We therefore mark it as a phony target:

```make
.PHONY: clean
```

This tells Make that `clean` is not a file. Without this line, a file called `clean` could cause Make to decide that the target was already up to date and skip the command.

The two commands to remember for this Makefile are:

```bash
make
make clean
```

## Makefiles in Units 1 and 2

The Makefiles used in Units 1 and 2 provide a separate test target for each activity.

At the top of the Makefile, the compiler and compiler flags are stored in variables:

```make
CC := gcc
CFLAGS := -Wall -Wextra -Wpedantic -std=c11
```

These are used in the compilation commands as:

```make
$(CC)
$(CFLAGS)
```

For example:

```make
$(CC) $(CFLAGS) activity1_hello.c -o activity1_hello.out
```

becomes:

```bash
gcc -Wall -Wextra -Wpedantic -std=c11 activity1_hello.c -o activity1_hello.out
```

### Choosing a test

Running `make` without naming a target runs the first ordinary target in the Makefile.

In the activity Makefiles, the first target is:

```make
test:
	@echo "Choose an activity test:"
	@echo "  make test1  Activity 1: Hello World"
	@echo "  make test2  Activity 2: Data Types"
	@echo "  make test3  Activity 3: Loops"
```

Therefore, running:

```bash
make
```

displays the list of available tests.

To build and test a particular activity, give the target name:

```bash
make test1
make test2
make test3
```

For example:

```make
test2: activity2_data_types.out
	@bash activity2_test.sh
```

When we run:

```bash
make test2
```

Make first checks whether the required program is available and up to date:

```text
activity2_data_types.out
```

If necessary, Make builds the program. It then runs:

```bash
bash activity2_test.sh
```

The `@` before a command stops Make from printing the command itself. The output produced by the command still appears.

### Phony targets

The Makefile contains:

```make
.PHONY: test test1 test2 test3 test4 test5 test6 clean
```

These targets do not create files called `test1`, `test2` or `clean`. They are names for commands that we want Make to run.

Declaring them as phony tells Make not to look for files with those names.

Without `.PHONY`, a file called `test2` could cause Make to decide that the target was already up to date and skip the test.

### Building the program

Each output file has its own build rule:

```make
activity2_data_types.out: activity2_data_types.c
	$(CC) $(CFLAGS) $< -o $@
```

Here:

- `activity2_data_types.out` is the target
- `activity2_data_types.c` is the dependency
- the second line is the compilation command

The recipe uses two automatic variables:

- `$<` means the first dependency
- `$@` means the target

For this rule:

```text
$< = activity2_data_types.c
$@ = activity2_data_types.out
```

The command therefore becomes:

```bash
gcc -Wall -Wextra -Wpedantic -std=c11 activity2_data_types.c -o activity2_data_types.out
```

Using `$<` and `$@` avoids repeating the filenames in every rule.

### Cleaning the project

The `clean` target removes the compiled programs:

```make
clean:
	rm -f *.out
```

Run it using:

```bash
make clean
```

The `*` in `*.out` is a wildcard, so this removes every file whose name ends in `.out`.

The main commands to remember are:

```bash
make
make test1
make test2
make clean
```

Running `make` shows the available tests. Running `make test1`, `make test2` and so on builds and tests the chosen activity.

Later, the STM32 projects will use CMake. A Makefile contains the build rules directly, while CMake describes the project and generates the files needed by the build system.