---
title: Command-Line Menu Example
parent: Command Line Basics
nav_order: 1
layout: default
---

# Command-Line Menu System in C

## Introduction

Command-line interfaces (CLI) are simple but can be made more user-friendly by adding menus. This example demonstrates a **menu-driven program in C** that allows the user to select from multiple options, perform tasks, and return to the main menu.

***

## Full Source Code (`main.c`)

```c
// ELEC2645 Unit 2 Activity 2 - Menu-Driven Program
#include <stdio.h>   // for printf(), scanf()
#include <stdlib.h>  // for exit()

// ---------- Function Prototypes ----------
void main_menu(void);
int get_user_input(void);
void select_menu_item(int input);
void print_main_menu(void);
void go_back_to_main(void);
void menu_item_1(void); // Square a number
void menu_item_2(void); // ASCII code -> character
void menu_item_3(void); // Celsius -> Fahrenheit
void menu_item_4(void); // Even/Odd

#define PI 3.14159f

// ---------- Main Function ----------
int main(void) {
    main_menu();
    return 0;
}

// ---------- Menu Functions ----------
void main_menu(void) {
    print_main_menu();
    int input = get_user_input();
    select_menu_item(input);
}

int get_user_input(void) {
    int input;
    printf("\nSelect item: ");
    scanf("%d", &input); // assume valid input for this activity
    return input;
}

void select_menu_item(int input) {
    switch (input) {
        case 1: menu_item_1(); break;
        case 2: menu_item_2(); break;
        case 3: menu_item_3(); break;
        case 4: menu_item_4(); break;
        case 6: printf("Exiting program...\n"); exit(0);
        default: printf("Invalid selection. Exiting...\n"); exit(1);
    }
}

void print_main_menu(void) {
    printf("\n----------- Main menu -----------\n");
    printf("\n\t1. Square a number\t\n");
    printf("\n\t2. ASCII code -> char\t\n");
    printf("\n\t3. Celsius -> Fahrenheit\n");
    printf("\n\t4. Even or Odd\t\t\n");
    printf("\n\t6. Exit\t\t\t\n");
    printf("---------------------------------\n");
}

void go_back_to_main(void) {
    char input;
    do {
        printf("\nEnter 'b' or 'B' to go back to main menu: ");
        scanf(" %c", &input);
    } while (input != 'b' && input != 'B');
    main_menu();
}

// ---------- Menu Item Functions ----------
void menu_item_1(void) {
    printf("\n>> Menu 1: Square a number\n");
    printf("Enter a number to square:\n");
    int num;
    scanf("%d", &num);
    printf("Result: %d\n", num * num);
    go_back_to_main();
}

void menu_item_2(void) {
    printf("\n>> Menu 2: ASCII code to character\n");
    int code;
    printf("Enter ASCII code (e.g. 65 for 'A'):\n");
    scanf("%d", &code);
    if (code < 0 || code > 127) {
        printf("Code out of ASCII range (0-127)\n");
    } else {
        printf("Character: '%c'\n", (char)code);
    }
    go_back_to_main();
}

void menu_item_3(void) {
    printf("\n>> Menu 3: Celsius to Fahrenheit\n");
    int celsius;
    printf("Enter temperature in Celsius:\n");
    scanf("%d", &celsius);
    float fahrenheit = (celsius * 9.0f / 5.0f) + 32.0f;
    printf("%d°C = %.2f°F\n", celsius, fahrenheit);
    go_back_to_main();
}

void menu_item_4(void) {
    printf("\n>> Menu 4: Even or Odd\n");
    int num;
    printf("Enter an integer:\n");
    scanf("%d", &num);
    if (num % 2 == 0) {
        printf("%d is Even\n", num);
    } else {
        printf("%d is Odd\n", num);
    }
    go_back_to_main();
}
```

***

## Detailed Explanation of Each Function

### **`main()`**

*   Entry point of the program.
*   Calls `main_menu()` to start the menu loop.

***

### **`main_menu()`**

*   Displays the menu using `print_main_menu()`.
*   Gets user input via `get_user_input()`.
*   Passes the input to `select_menu_item()` for processing.

***

### **`print_main_menu()`**

*   Prints a formatted menu with options:
    *   **1**: Square a number
    *   **2**: ASCII code → character
    *   **3**: Celsius → Fahrenheit
    *   **4**: Even or Odd
    *   **6**: Exit program

***

### **`get_user_input()`**

*   Prompts the user to enter an integer.
*   Uses `scanf()` to read the input.
*   Returns the integer to `main_menu()`.

***

### **`select_menu_item(int input)`**

*   Uses a `switch` statement to call the correct function based on user input.
*   If input is invalid, prints an error and exits.

***

### **`go_back_to_main()`**

*   After completing an operation, prompts the user to enter `'b'` or `'B'` to return to the main menu.
*   Loops until valid input is given.
*   Calls `main_menu()` again.

***

### **Menu Item Functions**

*   **`menu_item_1()`**: Squares an integer entered by the user.
*   **`menu_item_2()`**: Converts an ASCII code (0–127) to its corresponding character.
*   **`menu_item_3()`**: Converts Celsius temperature to Fahrenheit using the formula: `F = C x 9\5 + 32`
*   **`menu_item_4()`**: Checks if an integer is even or odd using `num % 2`.

***

## Example Output

    ----------- Main menu -----------
        1. Square a number
        2. ASCII code -> char
        3. Celsius -> Fahrenheit
        4. Even or Odd
        6. Exit
    ---------------------------------
    Select item: 1
    >> Menu 1: Square a number
    Enter a number to square:
    Result: 25
    Enter 'b' or 'B' to go back to main menu:

***

