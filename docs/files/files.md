---
title: Files
nav_order: 3
layout: default
---

Covering the basics of how to load and save data to files.

```c
#include <stdio.h>

int main(void) {
    // Open a file for writing
    FILE *f = fopen("love_for_c.txt", "w");

    if (!f) {
        printf("Could not open file!\n");
        return 1;
    }

    // Write your true feelings
    fprintf(f, "Dear C,\n");
    fprintf(f, "I love you because you trust me to know what I'm doing, even when I don't!\n");
    fprintf(f, "Yours truly,\nA Happy Programmer\n");

    // Close the file
    fclose(f);

    printf("Love letter saved to love_for_c.txt \n");
    return 0;
}
```
