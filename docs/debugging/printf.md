---
title: Printf Debugging
parent: Debugging
nav_order: 1
layout: default
---

# Printf debugging

One of the simplest debugging techniques is to print information while the program is running.

This is known as **printf debugging**.

The idea is simple:

1. Find the area where you think the bug might be.
2. Print the values of important variables.
3. Compare the program's behaviour with what you expected.

Once the bug has been fixed, the temporary debug output can be removed.

## Example

This function should calculate the sum of the numbers from 1 to `n`:

```c
int total_up_to(int n)
{
    int total = 0;

    for (int i = 1; i < n; i++) {
        total = total + i;
    }

    return total;
}
```

However:

```c
printf("%d\n", total_up_to(5));
```

prints:

```text
10
```

when we expected:

```text
15
```

We know there is a bug, but we do not yet know where.

## Printing variable values

We can add a debug print inside the loop:

```c
int total_up_to(int n)
{
    int total = 0;

    for (int i = 1; i < n; i++) {
        total = total + i;

        printf(
            "i=%d total=%d\n",
            i,
            total
        );
    }

    return total;
}
```

This prints:

```text
i=1 total=1
i=2 total=3
i=3 total=6
i=4 total=10
```

The debug output shows that the loop stops when `i` reaches 4.

That suggests the problem is probably in the loop condition.

## Finding the bug

The loop currently uses:

```c
i < n
```

which excludes the final value.

Changing it to:

```c
i <= n
```

fixes the problem:

```c
for (int i = 1; i <= n; i++) {
    total = total + i;
}
```

The function now produces the expected result.

## What should we print?

Useful things to print include:

- loop counters
- function parameters
- values being returned
- variables that are changing unexpectedly
- values used in conditions

For example:

```c
printf("i = %d\n", i);
printf("temperature = %.1f\n", temperature);
printf("state = %d\n", state);
```

Try to print enough information to understand what the program is doing without flooding the terminal with thousands of lines of output.

## Removing debug prints

Debug prints are usually temporary:

```c
printf("Debug: value = %d\n", value);
```

Once the problem has been found and fixed, remove the prints or disable them using techniques described on the Debug vs Release page.

## Printf or a debugger?

`printf()` debugging works everywhere and is often the quickest way to investigate a problem.

A debugger allows the program to be paused so that variables can be inspected without adding print statements.

In practice, experienced programmers use both approaches regularly.