---
title: Writing an FSM in C
parent: Finite State Machines (FSM)
nav_order: 2
layout: default
---

# Writing an FSM in C

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

An FSM in C needs three things: an `enum` that lists the states, one variable that holds the current state, and one loop that runs the action for that state and then picks the next one. This page turns the [Square, Cube and Exit design]({% link docs/fsm/fsm_intro.md %}) into code. It is the structure of Unit 2.1 Activity 2 and of `main.c` in the Unit 2 project template.

## List the states with an enum

```c
typedef enum {
    STATE_MAIN_MENU,
    STATE_SQUARE,
    STATE_CUBE,
    STATE_EXIT
} AppState;
```

Each state on the diagram becomes one name. Using an [enum]({% link docs/C_Basics/enums.md %}) for the states means:

- all the allowed modes are listed in one place
- the names say what they mean: `STATE_CUBE`, not `2`
- the debugger shows the name as you step through
- adding a mode starts with adding a name here
- there are no unexplained numbers in the code

## One variable holds the current state

```c
AppState state = STATE_MAIN_MENU;
```

This one line answers the question from [going back to the menu]({% link docs/menus/menu_flow.md %}): where is the current mode stored? It is stored here. The starting value is the start arrow on the diagram.

## One loop stays in control

```c
while (state != STATE_EXIT) {
    switch (state) {
        case STATE_MAIN_MENU:
            state = run_main_menu();
            break;

        case STATE_SQUARE:
            run_square_state();
            state = STATE_MAIN_MENU;
            break;

        case STATE_CUBE:
            run_cube_state();
            state = STATE_MAIN_MENU;
            break;

        case STATE_EXIT:
            break;

        default:
            printf("Unknown state. Returning to the main menu.\n");
            state = STATE_MAIN_MENU;
            break;
    }
}
```

Every time round, the loop does the same four things:

1. **Read** the state: the `switch` looks at `state`.
2. **Run** the action: the `case` for that state calls its function.
3. **Set** the next state: assign a new value to `state`.
4. **Repeat**: go round the loop again.

Each assignment to `state` is one arrow on the diagram. `state = STATE_MAIN_MENU;` after the square action is the "complete" arrow from `SQUARE` back to `MAIN_MENU`. The main menu is different: which arrow it follows depends on what the user types, so `run_main_menu()` works that out and returns the next state.

The `STATE_EXIT` case never runs, because the `while` condition stops the loop first. It is listed so that every state appears in the `switch`. The `default` case should never run either: it is there so that if a bug ever puts a value into `state` that is not one of the states, the program goes back to the menu instead of looping forever and doing nothing.

## Menu input becomes a transition

```c
AppState run_main_menu(void)
{
    int selection;

    print_main_menu();

    if (!read_integer("Select item: ", &selection)) {
        printf("Invalid input. Please enter a menu number.\n");
        return STATE_MAIN_MENU;
    }

    switch (selection) {
        case 1:
            return STATE_SQUARE;

        case 2:
            return STATE_CUBE;

        case 3:
            return STATE_EXIT;

        default:
            printf("Invalid menu selection.\n");
            return STATE_MAIN_MENU;
    }
}
```

The user typing `2` is the event. `run_main_menu()` turns it into a transition by returning `STATE_CUBE`, and `main()` stores that in `state`. An invalid selection returns `STATE_MAIN_MENU`, which is the invalid-selection arrow that loops back to the menu.

`read_integer()` is the `fgets()` plus `sscanf()` function from [user input]({% link docs/menus/user_input.md %}). The lecture slides and Activity 2 use `scanf()` instead, to keep the focus on the FSM, and assume the user always types a number. In a loop, that assumption matters: if you type a letter, `scanf()` leaves it waiting in the input, so the next `scanf()` fails on the same letter, and the menu prints forever.

The state actions do their job and return. They do not call the menu:

```c
void run_square_state(void)
{
    int value;

    printf("\n>> Square an integer\n");

    if (!read_integer("Enter an integer: ", &value)) {
        printf("Invalid integer. Returning to the menu.\n");
        return;
    }

    printf("Result: %d\n", value * value);
}
```

Even when the input is invalid, the function returns and the loop sends the program back to the menu.

## Complete example

```c
#include <stdio.h>

typedef enum {
    STATE_MAIN_MENU,
    STATE_SQUARE,
    STATE_CUBE,
    STATE_EXIT
} AppState;

AppState run_main_menu(void);
void print_main_menu(void);
void run_square_state(void);
void run_cube_state(void);
int read_integer(const char *prompt, int *value);

int main(void)
{
    AppState state = STATE_MAIN_MENU;

    while (state != STATE_EXIT) {
        switch (state) {
            case STATE_MAIN_MENU:
                state = run_main_menu();
                break;

            case STATE_SQUARE:
                run_square_state();
                state = STATE_MAIN_MENU;
                break;

            case STATE_CUBE:
                run_cube_state();
                state = STATE_MAIN_MENU;
                break;

            case STATE_EXIT:
                break;

            default:
                printf("Unknown state. Returning to the main menu.\n");
                state = STATE_MAIN_MENU;
                break;
        }
    }

    printf("Exiting application.\n");
    return 0;
}

AppState run_main_menu(void)
{
    int selection;

    print_main_menu();

    if (!read_integer("Select item: ", &selection)) {
        printf("Invalid input. Please enter a menu number.\n");
        return STATE_MAIN_MENU;
    }

    switch (selection) {
        case 1:
            return STATE_SQUARE;

        case 2:
            return STATE_CUBE;

        case 3:
            return STATE_EXIT;

        default:
            printf("Invalid menu selection.\n");
            return STATE_MAIN_MENU;
    }
}

void print_main_menu(void)
{
    printf("\n1. Square an integer\n");
    printf("2. Cube an integer\n");
    printf("3. Exit\n");
}

void run_square_state(void)
{
    int value;

    printf("\n>> Square an integer\n");

    if (!read_integer("Enter an integer: ", &value)) {
        printf("Invalid integer. Returning to the menu.\n");
        return;
    }

    printf("Result: %d\n", value * value);
}

void run_cube_state(void)
{
    int value;

    printf("\n>> Cube an integer\n");

    if (!read_integer("Enter an integer: ", &value)) {
        printf("Invalid integer. Returning to the menu.\n");
        return;
    }

    printf("Result: %d\n", value * value * value);
}

int read_integer(const char *prompt, int *value)
{
    char line[64];
    char extra;

    printf("%s", prompt);

    if (fgets(line, sizeof(line), stdin) == NULL) {
        return 0;
    }

    if (sscanf(line, "%d %c", value, &extra) != 1) {
        return 0;
    }

    return 1;
}
```

This produces (typing `2`, `3`, `x`, `3`):

```text
1. Square an integer
2. Cube an integer
3. Exit
Select item: 2

>> Cube an integer
Enter an integer: 3
Result: 27

1. Square an integer
2. Cube an integer
3. Exit
Select item: x
Invalid input. Please enter a menu number.

1. Square an integer
2. Cube an integer
3. Exit
Select item: 3
Exiting application.
```

The project template's `main.c` has the same FSM. There, the calculations live in `funcs.c` and `read_integer()` lives in `input.c`, as described in [splitting a menu program]({% link docs/projects/software_projects.md %}#splitting-a-menu-program).

## Trace one journey in the debugger

The [debugger]({% link docs/debugging/debugger.md %}) shows the FSM working. Put a breakpoint on the `switch (state) {` line and watch `state` in the **Variables** panel. Run the program, then press **Continue** each time it stops. Typing `2`, `3` and then `3` gives these stops:

| Stop | `state` | What happens before the next stop |
|---|---|---|
| 1 | `STATE_MAIN_MENU` | the menu prints and you type `2` |
| 2 | `STATE_CUBE` | the cube action runs and you type `3` |
| 3 | `STATE_MAIN_MENU` | the menu prints and you type `3`; the loop ends |

That is the journey MAIN_MENU, select 2, CUBE, complete, MAIN_MENU, select 3, EXIT, one arrow on the diagram at a time. The debugger shows the name `STATE_CUBE`, not a number.

The **Call Stack** panel is just as useful. Put a breakpoint in `run_square_state()` and choose square, then cube, then square again. On the second stop the stack is only:

```text
run_square_state()
main()
```

Compare this with the long list in [going back to the menu]({% link docs/menus/menu_flow.md %}). However many trips you make round the menu, the stack does not grow, because every action finishes and returns to the loop in `main()`.

## Common mistakes

### The state never changes

```c
case STATE_SQUARE:
    run_square_state();
    break;  // wrong: state is still STATE_SQUARE
```

Without `state = STATE_MAIN_MENU;`, the next time round the loop is still in the square state, so the action repeats forever. Typing `5`, `6`, `7`, `x` gives:

```text
>> Square an integer
Enter an integer: 5
Result: 25

>> Square an integer
Enter an integer: 6
Result: 36

>> Square an integer
Enter an integer: 7
Result: 49

>> Square an integer
Enter an integer: x
Invalid integer. Returning to the menu.

>> Square an integer
Enter an integer:
```

Even the "Returning to the menu" message is wrong, because returning to the menu is not up to `run_square_state()`. Every state needs to set or return a next state.

### A state function calls the menu

```c
void run_square_state(void)
{
    // ...
    run_main_menu();  // wrong
}
```

This is the problem from [going back to the menu]({% link docs/menus/menu_flow.md %}) creeping back in: control is nested again, and the state that `run_main_menu()` returns is thrown away. Finish the action and return to the loop; the loop decides what happens next.

### A missing `break`

If the `break` after the square case in the complete example is left out, the program falls through into the cube case and runs both actions. With `-Wextra`, GCC warns:

```text
fsm_menu.c:28:23: warning: this statement may fall through [-Wimplicit-fallthrough=]
   28 |                 state = STATE_MAIN_MENU;
      |                 ~~~~~~^~~~~~~~~~~~~~~~~
```

### Numbers instead of names

```c
case 2:
    return 2;  // wrong: means STATE_CUBE, but only by accident
```

C lets you use a number where an `enum` value is expected, and GCC does not warn, even with `-Wall -Wextra -Wpedantic`. It works until someone adds a state near the top of the `enum` and every number moves. Always use the names.

The main points to remember are:

- an `enum` lists the states, one variable holds the current state and one loop stays in control
- each time round, the loop reads the state, runs its action, sets the next state and repeats
- each assignment to `state` is one arrow on the diagram
- the main menu turns the user's input into a transition by returning the next state
- state actions finish and return to the loop; they never call the menu
- the debugger shows the state by name, and the call stack stays short
