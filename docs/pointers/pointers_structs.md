---
title: Structs with pointers
parent: Pointers
nav_order: 4
layout: default
---

# Structs and pointers

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

A struct can be passed to a function like any other variable.

When passed normally, the function receives a copy:

```c
typedef struct {
    int level;
    int hp;
} pokemon_t;

void heal_copy(pokemon_t pokemon)
{
    pokemon.hp = 50;
}
```

Calling this function does not change the original struct:

```c
pokemon_t pikachu = {
    .level = 12,
    .hp = 35
};

heal_copy(pikachu);
```

Only the local copy inside `heal_copy()` is changed.

## Passing a pointer to a struct

If the function needs to change the original struct, pass its address:

```c
void heal_pokemon(pokemon_t *pokemon)
{
    pokemon->hp = 50;
}
```

The caller uses `&` to pass the address:

```c
heal_pokemon(&pikachu);
```

The function now has a pointer to the original `pikachu` struct.

## The `->` operator

We use `.` when working directly with a struct variable:

```c
pikachu.hp = 35;
```

We use `->` when working through a pointer to a struct:

```c
pokemon->hp = 50;
```

This:

```c
pokemon->hp
```

is equivalent to:

```c
(*pokemon).hp
```

The `->` form is much easier to read, and its nearly always used.

So basically what we need to keep in mind is:

```text
struct variable     use .
pointer to struct   use ->
```

## Complete example

```c
#include <stdio.h>

typedef struct {
    char name[16];
    int level;
    int hp;
} pokemon_t;

void heal_pokemon(pokemon_t *pokemon);

int main(void)
{
    pokemon_t pikachu = {
        .name = "Pikachu",
        .level = 12,
        .hp = 35
    };

    printf(
        "%s HP before healing: %d\n",
        pikachu.name,
        pikachu.hp
    );

    heal_pokemon(&pikachu);

    printf(
        "%s HP after healing: %d\n",
        pikachu.name,
        pikachu.hp
    );

    return 0;
}

void heal_pokemon(pokemon_t *pokemon)
{
    pokemon->hp = 50;
}
```

This produces:

```text
Pikachu HP before healing: 35
Pikachu HP after healing: 50
```

The caller passes the address of the struct:

```c
heal_pokemon(&pikachu);
```

The function changes the original struct using:

```c
pokemon->hp = 50;
```

## Read-only struct pointers

If a function only needs to read the struct, use `const`:

```c
void print_pokemon(const pokemon_t *pokemon)
{
    printf(
        "%s, level %d, HP %d\n",
        pokemon->name,
        pokemon->level,
        pokemon->hp
    );
}
```

The function can read the members, but the compiler prevents it from changing them:

```c
pokemon->hp = 50;    // Compiler error
```

The caller still passes the address:

```c
print_pokemon(&pikachu);
```

Using a pointer avoids copying the complete struct. Using `const` makes it clear that the function will not modify the original.

## Checking for `NULL`

A function can check that the pointer is valid before dereferencing it:

```c
void heal_pokemon(pokemon_t *pokemon)
{
    if (pokemon == NULL) {
        return;
    }

    pokemon->hp = 50;
}
```

This prevents the function from attempting to use a null pointer.

The main points to remember are:

- passing a struct normally gives the function a copy
- pass the struct's address when the function needs to modify the original
- use `.` with a struct variable
- use `->` with a pointer to a struct
- use `const` when the function should only read the struct