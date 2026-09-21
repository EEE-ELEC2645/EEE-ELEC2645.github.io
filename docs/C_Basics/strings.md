---
title: Strings
parent: C Basics
nav_order: 7
layout: default
---

# Strings in C

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


Frankly speaking, strings in C are a hassle compared with most modern programming languages. Have fun! :D

C does not have a built-in string type. Instead, a string is stored as an array of `char` values ending with a special character called the **null terminator**:

```c
'\0'
```

For example, the string `"Hello"` is stored as:

```text
'H'  'e'  'l'  'l'  'o'  '\0'
```

The null terminator tells functions such as `printf` where the string ends. Without it, a function may continue reading beyond the end of the array.

## Declaring and initialising strings

We can create a string using a **string literal**:

```c
char greeting[] = "Hello";
```

The compiler automatically adds the null terminator, so `greeting` contains six elements:

```text
Index:       0     1     2     3     4      5
Character:  'H'   'e'   'l'   'l'   'o'   '\0'
```

Although `"Hello"` contains five visible characters, the array needs six elements because of the null terminator.

## Specifying the array size

We can specify the size explicitly:

```c
char name[5] = "Goku";
```

This array contains:

```text
'G'  'o'  'k'  'u'  '\0'
```

We must leave enough room for the null terminator. This is too small:

```c
char name[4] = "Goku";
```

The four visible characters fill the entire array, leaving no room for `'\0'`. The result is an array of characters, but not a correctly terminated C string.

## Be careful with non-ASCII text

Characters outside basic ASCII can take more than one byte to store. For example, this looks as though it contains two characters:

```c
char kanji[] = "悟空";
```

However, in UTF-8, each kanji normally requires three bytes. The array therefore needs seven elements:

```text
悟        空        \0
3 bytes  3 bytes  1 byte
```

The compiler can work out the required size when we use empty square brackets:

```c
char kanji[] = "悟空";    // Compiler chooses the array size
```

Be careful if you specify the size yourself:

```c
char kanji[3] = "悟空";   // Much too small in UTF-8!
```

A `char` stores one **byte**, not necessarily one displayed character. This also affects accented characters, emoji and many other writing systems.

For now, it is safest to let the compiler determine the array size when initialising non-ASCII strings:

```c
char greeting[] = "こんにちは";
```

Working properly with different text encodings is a much larger topic, but the main point here is not to assume that one character on screen takes one byte.

## Initialising a string manually

We can specify each character separately:

```c
char word[4] = { 'C', 'a', 't', '\0' };
```

When doing this, we must add the null terminator ourselves.

Notice the difference between single and double quotation marks:

```c
char letter = 'C';       // One character
char word[] = "Cat";     // A string
```

Single quotation marks represent one character. Double quotation marks represent a string literal.

## Creating an empty string

This creates an array where every element is initially zero:

```c
char buffer[32] = { 0 };
```

Since zero is the value used for the null terminator, `buffer` initially contains an empty string.

This is useful when creating space that will be filled with text later.

## Character arrays that are not strings

Not every `char` array is a string.

For example, this keypad stores individual characters:

```c
char keypad[4][3] = {
    { '1', '2', '3' },
    { '4', '5', '6' },
    { '7', '8', '9' },
    { '*', '0', '#' }
};
```

We can access and print one character using `%c`:

```c
printf("Button pushed: %c\n", keypad[1][2]);
```

This prints:

```text
Button pushed: 6
```

The rows are not strings because they do not end with `'\0'`. That is fine because we are treating the elements as individual characters rather than printing each row using `%s`.

## Printing strings

Use `%s` with `printf` to print a string:

```c
#include <stdio.h>

int main(void)
{
    char message[] = "Hello, C!";

    printf("%s\n", message);

    return 0;
}
```

The `%s` format tells `printf` to start at the first character and continue until it finds `'\0'`.

We can also use `puts`:

```c
puts(message);
```

`puts` prints the string and adds a newline automatically.

## Accessing individual characters

Because a string is an array, we can access each character using an index:

```c
char word[] = "Hello";

printf("%c\n", word[0]);    // H
printf("%c\n", word[1]);    // e
printf("%c\n", word[4]);    // o
```

We can also change individual characters:

```c
char word[] = "Cat";

word[0] = 'B';

printf("%s\n", word);       // Bat
```

Be careful not to overwrite the null terminator unless you add another one later.

## Looping through a string

We often process a string one character at a time:

```c
#include <stdio.h>

int main(void)
{
    char message[] = "Hello";

    for (size_t i = 0; message[i] != '\0'; i++) {
        printf("message[%zu] = %c\n", i, message[i]);
    }

    return 0;
}
```

The loop continues until it reaches the null terminator.

We do not print `'\0'` because the loop condition becomes false when it reaches that element.

## Reading a string with `scanf`

We can use `%s` with `scanf` to read a single word:

```c
char name[16];

scanf("%15s", name);
```

Unlike reading an integer, we do not use `&` before `name`:

```c
scanf("%15s", name);
```

The array name already gives `scanf` the address of its first element.

The number in `%15s` limits the input to 15 characters, leaving one element for the null terminator.

This would be unsafe:

```c
char name[16];

scanf("%s", name);
```

There is no limit on how many characters `scanf` may try to store. If the input is longer than the array, it writes beyond the end of the buffer. This is known as a **buffer overflow**.

There is another limitation: `%s` stops at whitespace. If the user enters:

```text
James Avery
```

only `"James"` is stored.

For reading a complete line, `fgets` is normally a better choice.

## Reading a line with `fgets`

The `fgets` function reads a line of text while respecting the size of the array:

```c
#include <stdio.h>

int main(void)
{
    char buffer[32];

    printf("Enter your name: ");

    if (fgets(buffer, sizeof(buffer), stdin) == NULL) {
        return 1;
    }

    printf("Hello %s", buffer);

    return 0;
}
```

The arguments are:

```c
fgets(buffer, sizeof(buffer), stdin);
```

- `buffer` is the array where the text will be stored
- `sizeof(buffer)` is the amount of space available
- `stdin` means the text is read from standard input, which is normally the terminal

Unlike `%s`, `fgets` can read spaces.

## The newline left by `fgets`

If there is enough room in the array, `fgets` keeps the newline produced when the user presses Enter.

If the user enters:

```text
Hello
```

the array will normally contain:

```text
'H'  'e'  'l'  'l'  'o'  '\n'  '\0'
```

This is why the previous example uses:

```c
printf("Hello %s", buffer);
```

rather than:

```c
printf("Hello %s\n", buffer);
```

The string already contains a newline.

Sometimes we want to remove it. One approach is to search through the string:

```c
for (size_t i = 0; buffer[i] != '\0'; i++) {
    if (buffer[i] == '\n') {
        buffer[i] = '\0';
        break;
    }
}
```

When the loop finds `'\n'`, it replaces it with `'\0'`, ending the string at that position.

The `<string.h>` library provides another way to do this using `strcspn`, which we will cover on the next page.

## Buffer size and long input

`fgets` reads at most one fewer character than the size supplied. The remaining element is used for the null terminator.

For example:

```c
char buffer[8];

fgets(buffer, sizeof(buffer), stdin);
```

This can store at most seven characters plus `'\0'`.

If the user enters more text than will fit, `fgets` stores only the part that fits. The remaining characters stay in the input stream and may be read by the next input operation.

This can cause confusing behaviour when several inputs are read one after another, so make sure the buffer is large enough for the input you expect.

## Common mistakes

### Forgetting the null terminator

This is an array of characters, but it is not a valid C string:

```c
char word[3] = { 'C', 'a', 't' };
```

Printing it with `%s` produces undefined behaviour because `printf` keeps looking for a null terminator beyond the end of the array.

This is a valid string:

```c
char word[4] = { 'C', 'a', 't', '\0' };
```

### Not leaving enough space

A five-character word needs an array with at least six elements:

```c
char word[6] = "Hello";
```

The extra element stores `'\0'`.

### Using `==` to compare strings

This does not compare the contents of two strings:

```c
if (word_1 == word_2) {
    // ...
}
```

Arrays and strings cannot be compared this way. The `<string.h>` library provides `strcmp` for comparing strings, which we will cover on the next page.

### Writing beyond the end of the array

C does not check whether an index is valid:

```c
char word[5] = "Cat";

word[10] = 'X';    // Invalid index
```

Writing beyond the end of an array produces undefined behaviour and may overwrite other data.