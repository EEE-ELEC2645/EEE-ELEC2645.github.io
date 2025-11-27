---
title: Debugging
nav_order: 7
layout: default
---

# Introduction to Debugging

Code rarely works perfectly the first time—and that’s normal. Even major companies release updates to fix mistakes. Debugging is not optional; it’s a core part of programming. Bugs in your code can lead to incorrect results, crashes, or security vulnerabilities, or in extreme cases [exploding rockets](https://en.wikipedia.org/wiki/Ariane_flight_V88#Launch_failure), [lost spacecraft](https://en.wikipedia.org/wiki/Mars_Climate_Orbiter#Cause_of_failure), or [radiation overdose](https://en.wikipedia.org/wiki/Therac-25).

If your code doesn’t work, **you made a mistake**. Learning to find and fix errors yourself is essential. Developing the problem-solving skills needed to understand why your code isn't working and to fix it is crucial to succeed as an engineer.

Debugging can be frustrating, but it builds experience and resilience. Good habits reduce errors:

*   **Style and structure your code**: Write readable code with clear comments to help avoid mistakes.
*   **Compile often**: Catch syntax errors early.

### Types of Bugs

*   **Compile-time bugs**: Syntax errors flagged by the compiler (e.g., missing semicolons). These are usually easy to fix, generally start with the first error message and work down the list.
*   **Run-time bugs**: Logic errors that occur while the program runs, much harder to find and fix.

```c
#include <stdio.h>

int main(void) {
    int count = 0;
    while (count < 10) {
        printf("I love C ❤️\n");
        count; // BUG: forgot to increment count!
    }
    return 0;
}
```

In the example above, the program compiles fine but when running it enters an infinite loop because `count` is never incremented. This is a classic example of a run-time bug.
