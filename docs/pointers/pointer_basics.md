---
title: Pointer basics
parent: Pointers
nav_order: 1
layout: default
---

# Pointer basics

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

A pointer is a variable that stores a memory address, unlike a normal variable which store values. For example:

```c
int pikachu_hp = 35;
```

The value `35` is stored somewhere in memory. The variable name `pikachu_hp` allows us to access that value without needing to know its memory address.

We can obtain the address using the `&` operator:

```c
&pikachu_hp
```

The `&` can be read as "The address of `pikachu_hp`"

## Declaring a pointer

A pointer that stores the address of an `int` is declared like this:

```c
int *target_hp;
```

The `*` in this declaration tells the compiler that `target_hp` is a pointer to an integer.

We can initialise it with the address of `pikachu_hp`:

```c
int pikachu_hp = 35;
int *target_hp = &pikachu_hp;
```

The types match:

```text
pikachu_hp     int
&pikachu_hp    address of an int
target_hp      pointer to an int
```

A pointer should have a type that matches the variable it targets:

```c
int value = 10;
int *int_pointer = &value;
float temperature = 21.5f;
float *float_pointer = &temperature;
```

## The two uses of `*`

The `*` symbol appears in two places when working with pointers.

In a declaration:

```c
int *target_hp;
```

it means: "`target_hp` is a pointer to an `int`"

In an expression:

```c
*target_hp
```

it means: "Access the integer stored at the address in `target_hp`".

The second use is called **dereferencing** the pointer.

It is slightly annoying that C uses the same symbol for both! Yet another thing we just have to get used to I'm afraid!

## Reading a value through a pointer

If `target_hp` points to `pikachu_hp`:

```c
int pikachu_hp = 35;
int *target_hp = &pikachu_hp;
```

we can read the value through the pointer:

```c
printf("%d\n", *target_hp);
```

This prints:

```text
35
```

Both expressions access the same value:

```c
pikachu_hp
*target_hp
```

The first uses the variable name. The second follows the stored address.

## Changing a value through a pointer

Dereferencing a pointer also allows us to change the value at the stored address:

```c
int pikachu_hp = 35;
int *target_hp = &pikachu_hp;

*target_hp = 50;
```

After the assignment, `pikachu_hp` contains `50`.

The pointer does not contain the HP value itself. It contains the address where the HP value is stored.

A useful way to think about the assignment is:

```text
Store 50 in the integer targeted by target_hp.
```

## Changing the pointer's target

A pointer is a variable, so the address it stores can be changed:

```c
int pikachu_hp = 35;
int eevee_hp = 28;

int *target_hp = &pikachu_hp;
```

At this point, `target_hp` stores the address of `pikachu_hp`.

We can change it to store Eevee's address:

```c
target_hp = &eevee_hp;
```

Notice that there is no `*` before `target_hp`. We are changing the address stored in the pointer.

We can then change the new target:

```c
*target_hp = 45;
```

This changes `eevee_hp`, because `target_hp` now points to Eevee's HP.

The distinction is:

```text
target_hp     the address stored in the pointer
*target_hp    the value at that address
&eevee_hp     the address of eevee_hp
```

## Complete example

```c
#include <stdio.h>

int main(void)
{
    int pikachu_hp = 35;
    int eevee_hp = 28;

    int *target_hp = &pikachu_hp;

    printf("Current target HP: %d\n", *target_hp);

    *target_hp = 50;
    target_hp = &eevee_hp;

    printf("Current target HP: %d\n", *target_hp);

    *target_hp = 45;

    printf("Pikachu HP: %d\n", pikachu_hp);
    printf("Eevee HP: %d\n", eevee_hp);
    return 0;
}
```

This produces:

```text
Current target HP: 35
Current target HP: 28
Pikachu HP: 50
Eevee HP: 45
```

## Printing an address

Use `%p` to print an address:

```c
printf("%p\n", (void *)&pikachu_hp);
```

We can also print the address stored in the pointer:

```c
printf("%p\n", (void *)target_hp);
```

For example:

```c
#include <stdio.h>

int main(void)
{
    int pikachu_hp = 35;
    int *target_hp = &pikachu_hp;

    printf("Address of pikachu_hp: %p\n", (void *)&pikachu_hp);
    printf("Address in target_hp:   %p\n", (void *)target_hp);
    return 0;
}
```

The two printed addresses should be the same because `target_hp` points to `pikachu_hp`.

The exact address will probably change each time the program runs.

The `(void *)` conversion is required when printing an address using `%p`. We do not need to examine `void` pointers in detail yet, or indeed at all! It just enough to remember there is some extra syntax we need to do to print a pointer address. 

## Null pointers

A pointer does not always need to target a variable immediately.

We can initialise it to `NULL`:

```c
int *target_hp = NULL;
```

`NULL` means that the pointer does not currently point to a valid object.

Before dereferencing a pointer that may be `NULL`, check it:

```c
if (target_hp != NULL) {
    printf("Target HP: %d\n", *target_hp);
}
```

Never dereference a null pointer:

```c
int *target_hp = NULL;

*target_hp = 50;    // Invalid
```

This produces undefined behaviour and will often cause the program to crash!

## Uninitialised pointers

Do not leave a pointer uninitialised:

```c
int *target_hp;
```

At this point, `target_hp` contains an indeterminate address, who knows where its pointing to!? 

Dereferencing it is invalid:

```c
*target_hp = 50;
```

Initialise a pointer with either a valid address or `NULL`

```c
int pikachu_hp = 35;
int *target_hp = &pikachu_hp;
//or 
int *target_hp = NULL;
```

## A useful pattern

To create a pointer:

```c
int value = 10;
int *pointer = &value;
```

To read through the pointer:

```c
int copy = *pointer;
```

To change the targeted value:

```c
*pointer = 20;
```

To change the pointer's target:

```c
pointer = &another_value;
```

To create a pointer with no current target:

```c
int *pointer = NULL;
```

Check for `NULL` before dereferencing a pointer that may not have a valid target.