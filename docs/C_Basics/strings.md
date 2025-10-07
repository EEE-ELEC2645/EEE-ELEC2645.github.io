---
title: Strings
parent: C Basics
nav_order: 7
layout: default
---

# Strings in C

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


## Introduction

Frankly speaking, strings in `C` are a hassle compared to other more modern programming languages...Have fun! :D

In `C`, a **string** is a sequence of characters stored in a **`char` array** that ends with a special terminator character `\0` (often called **NUL**). Unlike many languages, C has **no built-in string type**—strings are just arrays of `char` with a trailing `\0`. When other functions manipulate strings they look for the `\0` to know the end of the string has been reached.

---

## Declaring and initialising strings

### Using a string literal

This works similar to arrays where we can skip the size and the compiler will figure it out. Remember compiler adds the terminator, so `greeting` will be 6 long.

Things between `""` are known as *literals*

```c
char greeting[] = "Hello";   // 'H' 'e' 'l' 'l' 'o' '\0'
```

### Specifying the size explicitly

You can set the size, useful if you want to be clear, but you must leave room for `\0`

```c
char name[5] = "Goku";      // 4 letters + '\0'
char kanji[3] = "悟空";     // 2 letters + '\0' non ASCII characters work *to an extent*
```

### Manual initialisation

`C` Also lets you specify each element separately and you **must** put the `\0` at the end yourself if you want to use the array with other string functions. Note that as each element is a `char` we need to put each one between `''`.

```c
char word[4] = { 'C', 'a', 't', '\0' };
```

This is typically used to create an empty buffer or store `chars` when you are *certain* you are not going to print directly

```c
char buf[1024]={0}; // same as {'\0'}
char keypad[4][3] = {
    {'1','2','3'},
    {'4','5','6'},
    {'7','8','9'},
    {'*','0','#'}
};
printf("Button pushed:%c \n",keypad[1][2]);
```

---

## Printing strings

Use `printf` with `%s` or `puts`:

```c
#include <stdio.h>

int main(void) {
  char msg[] = "Hello, C!";
  printf("%s\n", msg);
  puts(msg);  // adds a newline automatically
  return 0;
}
```

---

## Reading strings unsafely - Bad!

We can use scanf("%s", ...) to read a string, similar to how we read numbers previously. It stops reading when it encounters a space, so it is only suitable for single words.

Notice that, unlike `%d` for integers, we do not need to use the `&` argument because the name of the string already represents its address.

```c
char name[5];
scanf("%s", name);  // DANGEROUS: no width limit, input could exceed buffer size
```

However, this is **extremely** bad practice because it does not prevent the input from exceeding the buffer size—a problem known as [buffer overflow](https://en.wikipedia.org/wiki/Buffer_overflow#). Buffer overflows can cause undefined behaviour, crashes, and are a common source of [security exploits](https://www.youtube.com/watch?v=1S0aBV-Waeo).

## Reading strings safely - Good!

Thankfully there are some safer ways we can read strings from user input.

First is by giving `scanf` a width limit, remembering to leave space for the `\0` in the string we are saving to:

```c
char name[5];
scanf("%4s", name);  // width limit must be 1 less than string length
```

The downside of this is that if we change the size of `name` we also need to remember to change the width limit in the `scanf`.

A better approach is to use the function fgets, which can read multiple words from the user. fgets reads a whole line (up to the Enter key), and you need to tell it where to store the text (the char array), how many characters to read at most, and where to read from. Usually, we use "standard input" (stdin), which means the keyboard.

```c
#include <stdio.h>

int main(void) {
  char buffer[32];

  printf("Enter your name: ");
  fgets(buffer, sizeof(buffer), stdin);
  printf("Hello %s\n",buffer);

  return 0;
}
```

> `fgets` keeps the newline if there’s space; `strcspn` finds it safely (no loop required).


## Summary

- A C string is a **`char` array ending with `\0`**.
- Always allocate space for the terminator and keep track of buffer sizes.
- Prefer `fgets` (or width-limited `scanf`) for input, and `snprintf` for bounded formatting.
- We can `<string.h>` functions, as we will see later

---
