---
title: Using a debugger
parent: Debugging
nav_order: 2
layout: default
---

# Using a debugger

`printf()` is often the quickest way to investigate a bug, but a debugger gives you a more powerful approach. Instead of printing values, we can pause the program and inspect its state while it is running.

Different IDEs have different debugger interfaces, but the basic workflow is usually the same.

## Example program

This page uses a small program that adds up the values in an array:

```c
#include <stdio.h>

int sum_values(const int values[], int count)
{
    int total = 0;

    for (int i = 0; i < count; i++) {
        total = total + values[i];
    }

    return total;
}

int main(void)
{
    int values[5] = { 10, 20, 30, 40, 50 };

    int total = sum_values(values, 5);
    printf("Total: %d\n", total);

    return 0;
}
```

This prints:

```text
Total: 150
```

The program must be built with debug information (`-g -O0`). The Makefiles in the lab repositories already do this, and [Debug vs release builds]({% link docs/debugging/debug_v_release.md %}) explains why.

## Breakpoints

A breakpoint tells the debugger to pause execution when a particular line is reached. In VS Code, click in the margin to the left of a line number to add one.

If we suspect the line inside the loop is causing a problem, we can place a breakpoint on it:

```c
total = total + values[i];
```

![The VS Code editor showing the for loop of sum_values(). A red dot in the margin to the left of the line total = total + values[i]; marks the breakpoint.]({{ "/assets/images/debugger_breakpoint_crop.png" | relative_url }})

When the program reaches the breakpoint, execution pauses before the line runs. Each time you press **Continue**, the program runs until it reaches the breakpoint again, which here means once per pass of the loop.

## Starting the debugger

The lab repositories and the Unit 2 project already have the debugger set up. With a breakpoint in place:

1. Open **Run and Debug** from the left-hand toolbar.
2. Choose a configuration from the drop-down list at the top of the panel. Each lab has one for every activity, and the Unit 2 project has **Debug Application** and **Debug Unit Tests**.
3. Press `F5`, or select the green play button next to the drop-down list.

![The Run and Debug panel in the Unit 2 project. The configuration drop-down list at the top is open, showing Debug Application (ticked) and Debug Unit Tests, with the green play button to its left.]({{ "/assets/images/debugger_launch_crop.png" | relative_url }})

The configuration first runs `make` to build the program with its Makefile, then starts it in the debugger. If the build fails, the debugger does not start, so fix any compiler errors first. The configurations are stored in `.vscode/launch.json`, and the build commands they run are in `.vscode/tasks.json`.

`F5` always starts the configuration shown in the drop-down list, even if a different file is open in the editor. Check it shows the activity you are working on.

> Do not use the play button at the top right of the editor. It comes from the C/C++ extension and compiles only the file that is open, without the Makefile. For a program split over several files, such as the Unit 2 project, the build stops with `undefined reference` errors, because the other `.c` files were never compiled.

## Inspecting variables

Once the program is paused, we can inspect the current values of variables. After pressing **Continue** three times, the program is on its fourth pass of the loop. The **Variables** panel shows the local variables `i` and `total`, and the parameters `values` and `count`. Inside `sum_values()`, `values` is a pointer (see [Arrays with pointers]({% link docs/pointers/pointers_arrays.md %})), so the Variables panel shows only its address. To see an element, add the expression `values[i]` to the **Watch** panel with the **+** button. Together they show:

```text
i = 3
total = 60
values[i] = 40
```

![VS Code paused at the breakpoint, with the line total = total + values[i]; highlighted in the editor. The Variables panel lists i = 3, total = 60, values = 0x7fffffffcfe0 (an address) and count = 5. Below it, the Watch panel shows values[i] = 40.]({{ "/assets/images/debugger_variables_crop.png" | relative_url }})

`total` is 60 because the first three values (10, 20 and 30) have been added so far. This allows us to compare the program's actual state with what we expected.

If a variable already contains an unexpected value, we know the bug occurred earlier.

The **Call Stack** panel shows how the program got here. At the breakpoint it lists `sum_values` above `main`, because `main()` called `sum_values()`. Clicking `main` in the Call Stack shows `main()`'s own variables.

![The Call Stack panel, marked Paused on breakpoint. The top entry is sum_values with its parameters, and the entry below it is main(), in main.c.]({{ "/assets/images/debugger_callstack_crop.png" | relative_url }})

## Stepping through code

Rather than running the whole program, a debugger can execute one line at a time. While the program is paused, VS Code shows a small toolbar at the top of the window:

![The VS Code debug toolbar. From left to right: a drag handle, then the Continue, Step Over, Step Into, Step Out, Restart and Stop buttons.]({{ "/assets/images/debugger_toolbar_crop.png" | relative_url }})

| Button | What it does |
| --- | --- |
| **Continue** | runs until the next breakpoint, or to the end of the program |
| **Step Over** | runs the current line, including any function it calls, and stops on the next line |
| **Step Into** | goes inside a function called on the current line |
| **Step Out** | finishes the current function and stops back in the caller |
| **Restart** | starts the program again from the beginning |
| **Stop** | ends the program and the debugging session |

Paused inside the loop, pressing **Step Over** a few times moves between the loop line and `total = total + values[i];`, and you can watch `i` and `total` change in the Variables panel.

This is often useful when:

- a loop behaves unexpectedly
- a variable changes when it should not
- a function returns the wrong value

## A typical debugging process

A useful approach is:

1. Reproduce the bug.
2. Guess where the problem might be.
3. Set a breakpoint nearby.
4. Start the program in the debugger.
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
