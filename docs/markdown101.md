---
title: Markdown Basics
nav_order: 2
layout: default
---

# Using Markdown for documentation

Markdown is great! All the `readme` and this site is written in it. A bit of familiarity would be extremely useful for your projects. 


 A more in depth look can be found [on the github documentation](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax) 


The following is based on a guide [here](https://gist.github.com/rt2zz/e0a1d6ab2682d2c47746950b84c0b6ee) and [here](https://github.com/lifeparticle/Markdown-Cheatsheet)


You can quickly check your markdown using [this site](https://markdown-it.github.io/)

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>


# Headings

```md
# Heading 1
## Heading 2
### Heading 3
#### Heading 4
##### Heading 5
###### Heading 6
```
<!-- omit in toc -->
# Heading 1
<!-- omit in toc -->
## Heading 2
<!-- omit in toc -->
### Heading 3
<!-- omit in toc -->
#### Heading 4
<!-- omit in toc -->
##### Heading 5
<!-- omit in toc -->
###### Heading 6

# Text styles

## Normal

```md
The quick brown fox jumps over the lazy dog.
```

The quick brown fox jumps over the lazy dog.

## Bold

Mac: <kbd>command+B</kbd> or Windows: <kbd>control+B</kbd>

```md
**The quick brown fox jumps over the lazy dog.**
__The quick brown fox jumps over the lazy dog.__
```

**The quick brown fox jumps over the lazy dog.**

## Italic

Mac: <kbd>command+I</kbd> or Windows: <kbd>control+I</kbd>

```md
*The quick brown fox jumps over the lazy dog.*
_The quick brown fox jumps over the lazy dog._
```

*The quick brown fox jumps over the lazy dog.*

## Bold and Italic

```md
**_The quick brown fox jumps over the lazy dog._**
***The quick brown fox jumps over the lazy dog.***
```
<!-- markdownlint-disable-next-line MD049 -->
**_The quick brown fox jumps over the lazy dog._**


## Strike-through

```md
~~The quick brown fox jumps over the lazy dog.~~
```

~~The quick brown fox jumps over the lazy dog.~~

# Code and Syntax Highlighting

## Inline Code

You can highlight code in-line text using back ticks ` which look like this ``int num = 0 ``

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

# Tables

Tables are one of the more complicated things in Markdown! You can either write it using HTML like this:

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

Or in Markdown like this:

```md

| Default    | Left align | Center align | Right align |
| ---------- | :--------- | :----------: | ----------: |
| 9999999999 | 9999999999 | 9999999999   | 9999999999  |
| 999999999  | 999999999  | 999999999    | 999999999   |
| 99999999   | 99999999   | 99999999     | 99999999    |
| 9999999    | 9999999    | 9999999      | 9999999     |

```

| Default    | Left align | Center align | Right align |
| ---------- | :--------- | :----------: | ----------: |
| 9999999999 | 9999999999 | 9999999999   | 9999999999  |
| 999999999  | 999999999  | 999999999    | 999999999   |
| 99999999   | 99999999   | 99999999     | 99999999    |
| 9999999    | 9999999    | 9999999      | 9999999     |


# Links

Very useful! There are a number of ways of doing this:

## Inline Links

```md
[The-Ultimate-Markdown-Cheat-Sheet](https://github.com/lifeparticle/Markdown-Cheatsheet)
```

[The-Ultimate-Markdown-Cheat-Sheet](https://github.com/lifeparticle/The-Ultimate-Markdown-Cheat-Sheet)

## Reference Links

```md
[The-Ultimate-Markdown-Cheat-Sheet][reference text]

[The-Ultimate-Markdown-Cheat-Sheet][1]

[Markdown-Cheat-Sheet]

[reference text]: https://github.com/lifeparticle/Markdown-Cheatsheet
[1]: https://github.com/lifeparticle/Markdown-Cheatsheet
[Markdown-Cheat-Sheet]: https://github.com/lifeparticle/Markdown-Cheatsheet
```

[The-Ultimate-Markdown-Cheat-Sheet][reference text]

[The-Ultimate-Markdown-Cheat-Sheet][1]

[Markdown-Cheat-Sheet]

[reference text]: https://github.com/lifeparticle/The-Ultimate-Markdown-Cheat-Sheet
[1]: https://github.com/lifeparticle/The-Ultimate-Markdown-Cheat-Sheet
[Markdown-Cheat-Sheet]: https://github.com/lifeparticle/The-Ultimate-Markdown-Cheat-Sheet


## Relative

```md
[Example of a relative link](rl.md)
```

[Example of a relative link](rl.md)

## Auto

```md
Visit https://github.com/
```

Visit https://github.com/

```md
Email at example@example.com
```

Email at example@example.com

## Enclosed

```md
<https://github.com/>
```

<https://github.com/>

## Highlight words and link it to a URL

```md
[BinaryTree](https://binarytree.dev/)
```

[BinaryTree](https://binarytree.dev/)

![Jun-15-2024 09-55-51](https://github.com/lifeparticle/Markdown-Cheatsheet/assets/1612112/68d9c7a9-6b05-472f-bbc6-c1e180674502)

# Images

Alt text and title are optional.

```md
![alt text](https://images.unsplash.com/photo-1415604934674-561df9abf539?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=100&q=80 "Title text")
```

![alt text](https://images.unsplash.com/photo-1415604934674-561df9abf539?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=100&q=80)

```md
![alt text][image]

[image]: https://images.unsplash.com/photo-1415604934674-561df9abf539?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=100&q=80 "Title text"
```

![alt text][image]

[image]: https://images.unsplash.com/photo-1415604934674-561df9abf539?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=100&q=80 "Title text"

```md
<img src="https://images.unsplash.com/photo-1415604934674-561df9abf539?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=2772&q=80" width="100" height="100" border="10"/>
```

<!-- markdownlint-disable-next-line MD013 -->
<img src="https://images.unsplash.com/photo-1415604934674-561df9abf539?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=2772&q=80" width="100" height="100" border="10" alt="alt text"/>

<img src="https://media.giphy.com/media/qLHzYjlA2FW8g/giphy.gif" alt="GIF of students in a grand hall joyfully throwing their graduation caps into the air in celebration, with large banners and lit torches decorating the background."/>

```md
<img src="https://media.giphy.com/media/qLHzYjlA2FW8g/giphy.gif" />
```

<img src="https://img.shields.io/badge/theultimatemarkdowncheatsheet-brightgreen.svg" alt="Green button with the text 'theultimatemarkdowncheatsheet' in white letters."/>

```md
<img src="https://img.shields.io/badge/theultimatemarkdowncheatsheet-brightgreen.svg" />
```

<img src="./supported_file_extensions/md.svg" alt="Animated SVG of the text 'Markdown-Cheatsheet' in white letters."/>

```md
<img src="./supported_file_extensions/md.svg" alt="Animated SVG of the text 'Markdown-Cheatsheet' in white letters."/>
```

[![BinaryTree](https://github.com/lifeparticle/lifeparticle/blob/master/gh_social_light.png)](https://binarytree.dev/)

```md
[![BinaryTree](https://github.com/lifeparticle/lifeparticle/blob/master/gh_social_light.png)](https://binarytree.dev/)
```

<!-- markdownlint-disable-next-line MD013 -->
<a href='https://binarytree.dev/' target='_blank'> <img src='https://github.com/lifeparticle/lifeparticle/blob/master/gh_social_light.png' alt="Logo of BinaryTree featuring a hexagonal icon and the text 'BINARY TREE' in capital letters to the right of the icon."/> </a>

```md
<a href='https://binarytree.dev/' target='_blank'> <img src='https://github.com/lifeparticle/lifeparticle/blob/master/gh_social_light.png' /> </a>
```

## Lists

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
