---
title: Pointers
nav_order: 4
layout: default
---

# Pointers

Pointers are one of the most powerful features in C, and also the feature most likely to break something if misused!

```c
#include <stdio.h>
int main(void)
{
    const char *choc_cake = "Chocolate Cake";   // a string literal
    const char *fork = choc_cake;               // fork points to cake

    printf("Fork is pointing at: %s\n", fork);

    // Now fork points to something else
    const char *carrot_cake = "Carrot Cake";
    fork = carrot_cake; 
    printf("Fork moved! Now pointing at: %s\n", fork);

    return 0;
}
```

Me when I first tried learning pointers

<img src="https://media0.giphy.com/media/v1.Y2lkPTc5MGI3NjExZ3M1NHY3dWwwMGl2c2o0Y3B2cXUyNWhiZXJ5OHlid2F0Zms2cGF0YSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/HteV6g0QTNxp6/giphy.gif" alt="An ancient meme which shows my age."/>