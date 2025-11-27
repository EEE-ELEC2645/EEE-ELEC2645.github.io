---
title: Printf Debugging
parent: Debugging
nav_order: 1
layout: default
---

# Debugging in C Using `printf`

## Introduction

The simplest way to track down run-time bugs in C is by printing variable values to the terminal using `printf()`. Watching the output as the program runs can help spot unexpected values.

***

## Example: Fibonacci Sequence

Let’s write a program to print the Fibonacci sequence (0, 1, 1, 2, 3, 5, 8, …).

Unfortunately, our initial implementation has a bug. We’ll use `printf` debugging to find and fix it.

```c
// fib_seed_bug.c
#include <stdio.h>

int main(void) {
    int n = 0;
    printf("Enter the number of terms in the Fibonacci sequence to print: ");
    scanf("%d", &n);

    int fn_minus1 = 1;
    int fn_minus2 = 0;

    printf("%d\n%d\n", fn_minus1, fn_minus2);

    for (int i = 2; i <= n; i++) {
        int fn = fn_minus1 + fn_minus2;
        printf("%d\n", fn);

        fn_minus2 = fn_minus1;
        fn_minus1 = fn;
    }

    return 0;
}

```

***

## Testing

Try compiling and run the program:

```bash
gcc -Wall -o fib fib_seed_bug.c
./fib
```

It compiles first time! Result! :D We shouldn't jinx it though...

When prompted, enter how many Fibonacci numbers to print.

If you enter `5`, you should see:

```bash
1
0
1
2
3
5
```

But wait, that’s not right! The Fibonacci sequence should start with `0` and `1`. Our first two printed values are swapped. And the loop printed one extra value (`5`) too many! Lets use `printf` debugging to find out what’s going wrong.

*** 

## Adding Debug Prints

We’ll add `printf` statements inside the loop to print the internal state of our variables each iteration:

```c

// fib_seed_debug.c
#include <stdio.h>

int main(void) {
    int n = 0;
    printf("Enter the number of terms in the Fibonacci sequence to print: ");
    scanf("%d", &n);

    int fn_minus1 = 1;
    int fn_minus2 = 0;

    printf("%d\n%d\n", fn_minus1, fn_minus2);

    for (int i = 2; i < n; i++) {
        int fn = fn_minus1 + fn_minus2;
        printf("Loop %d: fn_minus1 = %d, fn_minus2 = %d, fn = %d\n", i, fn_minus1, fn_minus2, fn);

        printf("%d\n", fn);

        fn_minus2 = fn_minus1;
        fn_minus1 = fn;
    }

    return 0;
}

```

In this case if we enter `5` again, we get:

```bash
1
0
Loop 2: fn_minus1 = 1, fn_minus2 = 0, fn = 1
1
Loop 3: fn_minus1 = 1, fn_minus2 = 1, fn = 2
2
Loop 4: fn_minus1 = 2, fn_minus2 = 1, fn = 3
3
Loop 5: fn_minus1 = 3, fn_minus2 = 2, fn = 5
5
```

Looking at this we can see that our initial seed values are swapped. `fn_minus1` should start at `0` and `fn_minus2` at `1`. Lets sort that out and check the output again.

```c
// fib_seed_fix.c
#include <stdio.h>

int main(void) {
    int n = 0;
    printf("Enter the number of terms in the Fibonacci sequence to print: ");
    scanf("%d", &n);

    int fn_minus1 = 0;  
    int fn_minus2 = 1; // Seed Bug fixed here

    printf("%d\n%d\n", fn_minus1, fn_minus2);

    for (int i = 2; i <= n; i++) {
        int fn = fn_minus1 + fn_minus2;
        printf("Loop %d: fn_minus1 = %d, fn_minus2 = %d, fn = %d\n", i, fn_minus1, fn_minus2, fn);

        printf("%d\n", fn);

        fn_minus2 = fn_minus1;
        fn_minus1 = fn;
    }

    return 0;
}
```

Now when we run it with `5` we get:

```bash
0
1
Loop 2: fn_minus1 = 0, fn_minus2 = 1, fn = 1
1
Loop 3: fn_minus1 = 1, fn_minus2 = 0, fn = 1
1
Loop 4: fn_minus1 = 1, fn_minus2 = 1, fn = 2
2
Loop 5: fn_minus1 = 2, fn_minus2 = 1, fn = 3
3
```

Which is correct! The Fibonacci sequence starts with `0` and `1`, and the subsequent numbers are correct too. However we are getting 6 numbers instead of 5 - this should set alarm bells ringing that we have and "off by one" error in our loop condition.

***

## Fixing the Loop Condition

Looking closely at the loop we can see that we are starting at `2` and going to `<= n`, meaning we are actually printing `n + 1` numbers. We can quickly fix this by changing the loop condition to `< n` instead of `<= n`.

```c
// fib_final.c
#include <stdio.h>

int main(void) {
    int n = 0;
    printf("Enter the number of terms in the Fibonacci sequence to print: ");
    scanf("%d", &n);

    int fn_minus1 = 0;  
    int fn_minus2 = 1; // Seed Bug fixed here

    printf("%d\n%d\n", fn_minus1, fn_minus2);

    for (int i = 2; i < n; i++) { // loop condition bug fixed here
        int fn = fn_minus1 + fn_minus2;
        printf("Loop %d: fn_minus1 = %d, fn_minus2 = %d, fn = %d\n", i, fn_minus1, fn_minus2, fn);

        printf("%d\n", fn);

        fn_minus2 = fn_minus1;
        fn_minus1 = fn;
    }

    return 0;
}
```

Now when we run it with `5` we get:
```bash
Enter the number of terms in the Fibonacci sequence to print: 5
0
1
Loop 2: fn_minus1 = 0, fn_minus2 = 1, fn = 1
1
Loop 3: fn_minus1 = 1, fn_minus2 = 0, fn = 1
1
Loop 4: fn_minus1 = 1, fn_minus2 = 1, fn = 2
2
```

Great! We have the correct Fibonacci sequence starting with `0` and `1`, and we are printing exactly `5` numbers as requested. We can now remove the debug prints by commenting out the line if we want a clean output. 
Trying a few more values of `n` confirms everything is working as expected for example `n = 10` gives:

```bash
0
1
1
1
2
3
5
8
13
21
```

***

Phew! Debugging complete. This shows the importance of predicting the output of the program before running it. If we had not known what the Fibonacci sequence should look like, we may not have spotted the errors as easily.

Looking at code littered with commented printf statements is not uncommon, they probably are a sign that the programmer has had to debug the code multiple times (mine certainly is).

Next we can look at how to use compiler flags to enable or disable debug prints without needing to comment them out manually.
