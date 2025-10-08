---
title: Markdown Basics
nav_order: 99
layout: default
---

# Using Markdown for documentation

Markdown is great! All the `readmes` in the Activities in this module and the site you are reading right now is written using it. A bit of familiarity would be extremely useful for your projects.

The following is based on guides found [here](https://gist.github.com/rt2zz/e0a1d6ab2682d2c47746950b84c0b6ee) and [here](https://github.com/lifeparticle/Markdown-Cheatsheet)

 A more in depth look can be found [on the github documentation](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax) but the basics here are more than enough for our needs.

You can quickly check your markdown by pasting it into [this site](https://markdown-it.github.io/)

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


# Headings

Heading levels set by `#`

```md
# Heading 1
## Heading 2
### Heading 3
#### Heading 4
##### Heading 5
###### Heading 6
```

Will look like this:


# Heading 1
{: .no_toc }
## Heading 2
{: .no_toc }
### Heading 3
{: .no_toc }
#### Heading 4
{: .no_toc }
##### Heading 5
{: .no_toc }
###### Heading 6
{: .no_toc }

# Text styles

## Normal

```md
 C programming is my favourite.
```

 C programming is my favourite.

## Bold


```md
**C programming is my favourite.**
__ C programming is my favourite.__
```

**C programming is my favourite.**

## Italic

```md
*C programming is my favourite.*
_C programming is my favourite._
```

* C programming is my favourite.*

## Bold and Italic

```md
**_ C programming is my favourite._**
```
<!-- markdownlint-disable-next-line MD049 -->
**_C programming is my favourite._**


## Strike-through

```md
~~C programming is my favourite..~~
```

~~C programming is my favourite.~~

# Code and Syntax Highlighting

## Inline Code

You can highlight code in-line text by surrounding the text with back ticks `` (next to the 1 key) which look like this `int num = 0`.

## Code block

Blocks of code are typically fenced by lines with three back-ticks ```, you can specify the language next to the first ticks

````md
```c
int array[5] ={1,2,3,4,5};
float result = 0.0;
```
````

Will give you the code block with syntax highlighted:

```c
int array[5] ={1,2,3,4,5};
float result = 0.0;
```

# Lists

## Ordered

```md
1. One
2. Two
3. Three
```

1. One
2. Two
3. Three

## Unordered

```md
- 1
- 2
- 3
```

- 1
- 2
- 3

# Horizontal Rule

```md
---
```

---

# Tables

Tables are one of the more complicated things in Markdown!

You can either write it directly in Markdown like this:

```md

| Default    | Left align | Center align | Right align |
| ---------- | :--------- | :----------: | ----------: |
| 9999999999 | 9999999999 | 9999999999   | 9999999999  |
| 999999999  | 999999999  | 999999999    | 999999999   |

```

| Default    | Left align | Center align | Right align |
| ---------- | :--------- | :----------: | ----------: |
| 9999999999 | 9999999999 | 9999999999   | 9999999999  |
| 999999999  | 999999999  | 999999999    | 999999999   |

 or using HTML like this which handles text wrapping better:

```md
<table>
<tr>
<td width="33%">
The quick brown fox jumps over the lazy dog.
</td>
<td width="33%">
The quick brown fox jumps over the lazy dog.
</td>
<td width="33%">
The quick brown fox jumps over the lazy dog.
</td>
</tr>
</table>
```

<table>
<tr>
<td width="33%">
The quick brown fox jumps over the lazy dog.
</td>
<td width="33%">
The quick brown fox jumps over the lazy dog.
</td>
<td width="33%">
The quick brown fox jumps over the lazy dog.
</td>
</tr>
</table>

# Links

Very useful! There are a number of ways of doing this:

## Inline Links

```md
[ELEC2645 Website](https://eee-elec2645.github.io/)
```

[ELEC2645 Website](https://eee-elec2645.github.io/)

## Reference Links

```md
In the body of the text
[ELEC2645 Website][elec2645web]

Then later in the document you can put the reference 
[elec2645web]: https://eee-elec2645.github.io/

```

In the body of the text
[ELEC2645 Website][elec2645web2]

Then later in the document you can put the `[]: ` bit, normally at the bottom so it doesn't matter if it is rendered or not

[elec2645web2]: https://eee-elec2645.github.io/


## Relative link

```md
[Example of a relative link](assets/images/link.png)
```

[Example of a relative link](assets/images/link.png)



# Images

Follow the same format as links but we add ! before the brackets, like `![]()`. The Alt text and title text are optional but encouraged (certainly on Bluesky!).

```md
![Cat as a Service](https://cataas.com/cat "A different cat each time")
```

![Cat as a Service](https://cataas.com/cat "A different cat each time")

It is not possible to scale or resize the images using Markdown by itself, but we can easily use a bit of `html` 


```md
<img src="https://cataas.com/cat" width="100" height="100" border="10" alt="A different cat each time"/>
```

<img src="https://cataas.com/cat" width="100" height="100" border="10" alt="A different cat each time"/>


We can also (over)use `.gifs`

```md
<img src="https://media.giphy.com/media/qLHzYjlA2FW8g/giphy.gif" alt=An ancient meme which shows my age. />
```


<img src="https://media4.giphy.com/media/v1.Y2lkPTc5MGI3NjExZ3B2aGNlcXN3ZWw1bHlhdG82eW51azE4NTRtN3hta3g5c3pqbmF3ZyZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/Hcw7rjsIsHcmk/giphy.gif" alt="An ancient meme which shows my age."/>



# Happy Coding! 

<table>
<tr>
<td width="50%">
<img src="https://media4.giphy.com/media/v1.Y2lkPTc5MGI3NjExZnhsN2Z3bHZnOXM4Y3I0ZmJnaTU3ZDl1a2VxaTZ3OGhvNnJtdGd3OSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/lJNoBCvQYp7nq/giphy.gif" alt=An ancient meme which shows my age. />
</td>
<td width="50%">
<img src="https://media2.giphy.com/media/v1.Y2lkPTc5MGI3NjExbTZudXJsb2dpMXJ6djM0ZWNsNHdwNDFoMXgyaHBtOXFuZ2JxNWJ2MCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/SwImQhtiNA7io/giphy.gif" alt=An ancient meme which shows my age. />
</td>
</tr>
</table>