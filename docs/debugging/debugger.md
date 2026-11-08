---
title: Using a Debugger
parent: Debugging
nav_order: 2
layout: default
---

# Using a debugger

While `printf()` is often the quickest way to investigate a bug using a debugger provides another more powerful approach. Instead of printing values, we can pause the program and inspect its state while it is running.

Different IDEs have different debugger interfaces, but the basic workflow is usually the same.

## Breakpoints

A breakpoint tells the debugger to pause execution when a particular line is reached.

For example:

```c
int total = total + values[i];
```

If we suspect this line is causing a problem, we can place a breakpoint on it.

When the program reaches the breakpoint, execution pauses before the line runs.

## Inspecting variables

Once the program is paused, we can inspect the current values of variables.

For example:

```text
i = 3
total = 60
values[i] = 40
```

This allows us to compare the program's actual state with what we expected.

If a variable already contains an unexpected value, we know the bug occurred earlier.

## Stepping through code

Rather than running the whole program, a debugger can execute one line at a time.

For example:

```c
int total = total + values[i];
i++;
```

We can observe exactly when each variable changes.

This is often useful when:

- a loop behaves unexpectedly
- a variable changes when it should not
- a function returns the wrong value

## A typical debugging process

A useful approach is:

1. Reproduce the bug.
2. Guess where the problem might be.
3. Set a breakpoint nearby.
4. Run the program.
5. Inspect the variables.
6. Step through the code.
7. Find the first point where something unexpected happens.
8. Fix the problem and test again.

The important question is:

> What is the first thing the program does that I did not expect?

The answer is often very close to the real bug.

## Printf or debugger?

Both approaches are useful.

`printf()` debugging:

- quick
- works everywhere
- great for small programs

Debugger:

- pauses execution
- shows variable values directly
- avoids adding temporary print statements
- useful for larger programs

Experienced programmers regularly use both techniques.