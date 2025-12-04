---
title: Debug vs Release
parent: Debugging
nav_order: 3
layout: default
---

# Debug vs. Release in C

## Introduction

Software projects typically have two configurations:

*   **Debug**: Used during development and testing. Includes debug information in the executable (using the `-g` compile flag needed for gdb) and may run tests. These builds are not optimized, making debugging easier but resulting in larger, slower executables.

*   **Release**: Used when the project is finished. Debug information is excluded, tests are not run, and optimizations (e.g., `-O2`) are applied to reduce size or improve performance.

We will now look at how to easily **switch off** tests and our debug prints when we do not want to run them.

***

## Using the Preprocessor for Conditional Compilation

When running tests, you might call a function like this:

```c
int main() {
    // test the function
    run_sum_tests();

    // rest of your very important program
}
```

`run_sum_tests()` contains various test cases to verify the correctness of your code, before proceeding with the main program logic.

If you are ready for release, you could manually remove or comment out these lines. For small projects, this is easy. But for large projects, this is impractical.

Instead, we can use the **`#define` preprocessor directive** and wrap test calls like this:

```c
int main() {
    // test the function
#ifdef DEBUG
    run_sum_tests();
#endif

    // rest of your very important program
}
```

Now when you compile your program, the compiler will include the test function call only if `DEBUG` is defined.

## Defining `DEBUG`

The best practice is to define `DEBUG` **at compile time**, not in the source code (like using `#define DEBUG`). This keeps your code clean and makes switching between debug and release easy.

### Debug Build (tests included):

We can define a variable at compile time using the `-D MACRO` flag. For example, to define `DEBUG`, compile with:

```bash
gcc -Wall -g -D DEBUG -o main.exe main.c
```

This is the same as having `#define DEBUG` at the top of your source file, but it keeps your source code cleaner. The `-g` flag includes debug information for use with debuggers like `gdb` (more on that later).

### Release Build (tests excluded):

For release builds we omit the `-D DEBUG` and `-g`flags and  also optionally add optimization flags like `-O2`:

```bash
gcc -Wall -O2 -o main.exe main.c
```

## Using a Debug Flag - Fibonacci Example 

Instead of commenting out debug prints later, use a preprocessor flag:

```c
#include <stdio.h>

int main(void) {
    int n = 0;
    printf("Enter the number of terms in the Fibonacci sequence to print: ");
    scanf("%d", &n);

    int fn_minus1 = 0;
    int fn_minus2 = 1;
    int fn = 1;

    printf("%d\n%d\n", fn_minus1, fn_minus2);

    for (int i = 2; i < n; i++) {
        fn_minus2 = fn_minus1;
        fn_minus1 = fn;
        fn = fn_minus1 + fn_minus2;

        printf("%d\n", fn);

#ifdef DEBUG
        printf("[DEBUG] fn=%d fn-1=%d fn-2=%d\n", fn, fn_minus1, fn_minus2);
#endif
    }

    return 0;
}
```

Compile with debug enabled:

```bash
gcc -Wall -D DEBUG -o fib fib.c
```
