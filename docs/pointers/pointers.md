---
title: Pointers
nav_order: 5
layout: default
---

# Pointers

Pointers are one of the most useful features in C, and also one of the easiest ways to break something if used incorrectly!

What makes a pointer special is that it stores the *address* of another variable. We can use this address to access or change the original value:

```c
#include <stdio.h>

int main(void)
{
    int pikachu_hp = 35;
    int eevee_hp = 28;

    // Initially target Pikachu's HP
    int *target_hp = &pikachu_hp;

    *target_hp = 50;

    // Change the target to Eevee's HP
    target_hp = &eevee_hp;

    *target_hp = 45;

    printf("Pikachu HP: %d\n", pikachu_hp);
    printf("Eevee HP: %d\n", eevee_hp);
    return 0;
}
```

This produces:

```text
Pikachu HP: 50
Eevee HP: 45
```

The pointer initially targets `pikachu_hp`, so the first assignment changes Pikachu's HP. The pointer is then changed to target `eevee_hp`, so the second assignment changes Eevee's HP.

Do not worry if the syntax looks strange at first. Everyone finds pointers confusing when they first meet them. And probably the second time. And sometimes the twentieth time! :D

Me when I first tried learning pointers

<img src="https://media0.giphy.com/media/v1.Y2lkPTc5MGI3NjExZ3M1NHY3dWwwMGl2c2o0Y3B2cXUyNWhiZXJ5OHlid2F0Zms2cGF0YSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/HteV6g0QTNxp6/giphy.gif" alt="An ancient meme which shows my age."/>

Me when I tried learning pointers for the 20th time

<img src="https://media0.giphy.com/media/v1.Y2lkPTc5MGI3NjExZ3M1NHY3dWwwMGl2c2o0Y3B2cXUyNWhiZXJ5OHlid2F0Zms2cGF0YSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/HteV6g0QTNxp6/giphy.gif" alt="An ancient meme which shows my age."/>