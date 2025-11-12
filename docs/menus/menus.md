---
title: Command Line Basics
nav_order: 6
layout: default
---

# Command Line Basics

```c
#include <stdio.h>

void print_menu(void) {
    printf("\n=== CLI Café Menu ===\n");
    printf("1. 🍫 Chocolate Cake\n");
    printf("2. 🥕 Carrot Cake\n");
    printf("3. 🍪 Cookies\n");
    printf("4. ☕ Coffee\n");
    printf("5. Exit\n");
    printf("=====================\n");
}

int main(void) {
    int choice;
    do {
        print_menu();
        printf("Select your treat: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: printf("You chose Chocolate Cake! 🍫\n"); break;
            case 2: printf("You chose Carrot Cake! 🥕\n"); break;
            case 3: printf("You chose Cookies! 🍪\n"); break;
            case 4: printf("You chose Coffee! ☕\n"); break;
            case 5: printf("Goodbye! Come back soon! 👋\n"); break;
            default: printf("Oops! Invalid choice. Try again.\n");
        }
    } while (choice != 5);

    return 0;
}
```