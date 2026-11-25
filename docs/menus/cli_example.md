---
title: A first menu
parent: Command Line Basics
nav_order: 1
layout: default
---

# A first menu

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

A menu program does three things: it prints the options, reads the user's choice and acts on it. Think of it as a café where the waiter is `printf()` and the order pad is `scanf()`.

## Printing the menu

Put the menu in its own function. Most programs show the menu more than once, and a function means the menu text is written in one place:

```c
void print_menu(void)
{
    printf("=== CLI Cafe ===\n");
    printf("1. Chocolate cake\n");
    printf("2. Carrot cake\n");
    printf("3. Cookies\n");
    printf("4. Coffee\n");
    printf("================\n");
}
```

## Reading the choice

`scanf()` with `%d` reads a whole number and stores it at the address of `choice`:

```c
int choice = 0;

printf("Select your treat: ");
scanf("%d", &choice);  // assumes the user types a whole number
```

`choice` starts at `0` so that it always has a known value, even if `scanf()` cannot read a number. For now we assume the user types a number. [User input]({% link docs/menus/user_input.md %}) shows how to handle everything else.

## Acting on the choice

A `switch` statement picks one action for each menu number. Anything that is not on the menu goes to `default`:

```c
switch (choice) {
    case 1:
        printf("You chose chocolate cake.\n");
        break;
    // ...one case for each option...
    default:
        printf("Sorry, %d is not on the menu.\n", choice);
        break;
}
```

[Conditionals]({% link docs/C_Basics/conditionals.md %}) covers `switch` in more detail.

## Complete example

```c
#include <stdio.h>

void print_menu(void);

int main(void)
{
    int choice = 0;

    print_menu();
    printf("Select your treat: ");
    scanf("%d", &choice);  // assumes the user types a whole number

    switch (choice) {
        case 1:
            printf("You chose chocolate cake.\n");
            break;
        case 2:
            printf("You chose carrot cake.\n");
            break;
        case 3:
            printf("You chose cookies.\n");
            break;
        case 4:
            printf("You chose coffee. Good luck with the lab!\n");
            break;
        default:
            printf("Sorry, %d is not on the menu.\n", choice);
            break;
    }

    return 0;
}

void print_menu(void)
{
    printf("=== CLI Cafe ===\n");
    printf("1. Chocolate cake\n");
    printf("2. Carrot cake\n");
    printf("3. Cookies\n");
    printf("4. Coffee\n");
    printf("================\n");
}
```

This produces (typing `4`):

```text
=== CLI Cafe ===
1. Chocolate cake
2. Carrot cake
3. Cookies
4. Coffee
================
Select your treat: 4
You chose coffee. Good luck with the lab!
```

## Common mistakes

### Forgetting `break`

Without `break`, the program carries on into the next case. If the `break` after chocolate cake is missing, typing `1` produces:

```text
Select your treat: 1
You chose chocolate cake.
You chose carrot cake.
```

Two cakes for the price of one, but not what you asked for. Compiling with `-Wextra` warns you about this:

```text
warning: this statement may fall through [-Wimplicit-fallthrough=]
```

### Not giving `choice` a starting value

If `choice` is declared as `int choice;` and the user types a word, `scanf()` leaves `choice` unchanged. It then holds whatever happened to be in memory, so the program might print any number. Giving it a starting value makes the result predictable.

### The menu and the `switch` disagree

If you add an option to `print_menu()` but forget its `case`, choosing it goes to `default`. When you add or remove an option, change both.

## Two problems with the café

The café works, but it has two problems:

1. **It serves one customer and closes.** The program ends after one choice. A real menu should come back after each option. [Going back to the menu]({% link docs/menus/menu_flow.md %}) shows how the Unit 2.1 Activity 1 menu does this, and why it gets messy as the program grows.
2. **It cannot cope with unexpected input.** If you order `chips`, `scanf()` cannot read a number, `choice` keeps its starting value and the program prints:

   ```text
   Sorry, 0 is not on the menu.
   ```

   [User input]({% link docs/menus/user_input.md %}) shows how to read input safely and give a helpful error message.

The main points to remember are:

- print the menu from its own function
- give `choice` a starting value before reading into it
- use a `switch` with a `break` after every case and a `default` for anything unexpected
- keep the menu text and the `case` labels in step
