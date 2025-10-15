---
title: Functions
nav_order: 2
layout: default
---

# Functions

When you want to avoid typing the same thing over and over!

```c
#include <stdio.h>

// Prototype
void express_love_for_c(void);

int main(void) {
    express_love_for_c();
    return 0;
}

// Definition
void express_love_for_c(void) {
    printf("I love C because it's fun! :D :D :D\n");
}
```
