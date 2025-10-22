---
title: Structs with Pointers
parent: Pointers
nav_order: 3
layout: default
---

## Pointers and `struct`

C doesn’t have classes, but it does have `struct`.  
When you have a `struct` **object**, use `.` to access members
When you have a **pointer to a struct**, use `->`.

Below we model a very small Pokémon record and show:

- a direct (stack) object,
- a pointer to that existing object,
- how to pass a pointer to a function to modify the original object,
- and how passing **by value** differs from passing **by pointer**.

> **Note:** In C, **arrays** decay to pointers when passed to functions, but **structs do not decay**. You either pass a *copy* of the struct (by value), or you pass a *pointer* to it (e.g., `&obj`). This example demonstrates both.

---

### Example

```c
// struct_pointer_pokemon_stack.c
#include <stdio.h>
#include <string.h>

#define MAX_NAME  16
#define MAX_TYPE  12

struct Pokemon {
    char name[MAX_NAME];  // e.g. "Pikachu"
    char type[MAX_TYPE];  // e.g. "Electric"
    unsigned level;       // e.g. 25
    unsigned hp;          // current HP
};

static void print_pokemon(const struct Pokemon *p)
{
    if (!p) return;
    printf("Pokemon{name=\"%s\", type=\"%s\", level=%u, hp=%u}\n",
           p->name, p->type, p->level, p->hp);
}

/* Mutates the original via pointer */
static void level_up(struct Pokemon *p, unsigned delta)
{
    if (!p) return;
    p->level += delta;  // uses '->' because p is a pointer
}

/* Mutates a temporary copy (caller won't see changes) */
static void level_up_by_value(struct Pokemon p, unsigned delta)
{
    p.level += delta;   // uses '.' because p is a struct (a copy)
    print_pokemon(&p);  // show the change only inside this function
}

int main(void)
{
    // 1) Direct object use '.'
    struct Pokemon pika = {0};
    strcpy(pika.name, "Pikachu");
    strcpy(pika.type, "Electric");
    pika.level = 12;
    pika.hp    = 34;
    print_pokemon(&pika);

    // 2) Pointer to existing object: use '->'
    struct Pokemon *ptr = &pika; // take the address with '&'
    ptr->level += 1;             // Pikachu levels up
    ptr->hp    += 6;             // small heal
    print_pokemon(ptr);

    // 3) Pass pointer to a function to mutate (change) the original
    level_up(&pika, 3);          // modifies pika in place
    print_pokemon(&pika);

    // 4) Pass by value (copy): caller won't see the change
    level_up_by_value(pika, 10); // changes only the local copy
    print_pokemon(&pika);        // unchanged relative to previous line

    return 0;
}

```

This code produces:

```
Pokemon{name="Pikachu", type="Electric", level=12, hp=34}
Pokemon{name="Pikachu", type="Electric", level=13, hp=40}
Pokemon{name="Pikachu", type="Electric", level=16, hp=40}
Pokemon{name="Pikachu", type="Electric", level=26, hp=40}
Pokemon{name="Pikachu", type="Electric", level=16, hp=40}
```

## Key Points

- **Member access**
  - `obj.field` uses `.` because `obj` is **not** a pointer.
  - `ptr->field` uses `->` because `ptr` **is** a pointer (e.g., `ptr = &obj`).

- **Passing to functions**
  - `void f(struct Pokemon p)` receives a **copy**. Modifications affect only the local copy.
  - `void f(struct Pokemon *p)` receives a **pointer**. Modifications via `p->...` affect the **original** object in the caller.

- **No “decay” for structs**
  - Unlike arrays, **structs do not decay to pointers** automatically. You must explicitly pass `&obj` to functions that take a pointer.

- **Arrays inside the struct**
  - Fields like `char name[MAX_NAME]` are arrays. When you pass `p->name` to functions expecting a C‑string (e.g., `printf("%s", ...)`), that expression **does** decay to pointer `char *`, like we saw with normal arrays.

- **Designated initializers (optional)**
  - As we saw previously, you can avoid string copies entirely using string literals:

    ```c
    struct Pokemon pika = {
        .name  = "Pikachu",
        .type  = "Electric",
        .level = 12,
        .hp    = 34
    };
    ```
