---
title: Markdown basics
parent: Tools
nav_order: 3
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
[Markdown Guide](https://www.markdownguide.org/)
```

[Markdown Guide](https://www.markdownguide.org/)

## Reference Links

```md
In the body of the text
[Markdown Guide][mdguide]

Then later in the document you can put the reference
[mdguide]: https://www.markdownguide.org/
```

In the body of the text
[Markdown Guide][mdguide2]

Then later in the document you can put the `[]: ` bit, normally at the bottom so it doesn't matter if it is rendered or not

[mdguide2]: https://www.markdownguide.org/

## Relative links

A relative link points to another file in the same repository, using its path from the Markdown file you are writing in. This is how a README links to an activity guide, a design document or a diagram in your own repository. It keeps working when the repository is copied or renamed, which a full `https://github.com/...` link would not.

For a repository laid out like this:

```text
my_project/
├── README.md
├── docs/
│   └── wiring.md
└── images/
    └── fsm_diagram.png
```

`README.md` can link to the wiring notes with:

```md
[Wiring notes](docs/wiring.md)
```

and `docs/wiring.md` can link back up one folder with `..`:

```md
[Back to the README](../README.md)
```

And here is an example of a relative link on this website:

[Example of a relative link]({{ "/assets/images/link.png" | relative_url }})

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

## Images in your own repository

Images do not have to come from another website. Screenshots, photos of your hardware and diagrams can be saved in your repository and shown in your README with a relative path, exactly like a relative link.

Using the repository layout above, `README.md` shows the state diagram with:

```md
![State diagram for the main menu](images/fsm_diagram.png)
```

and `docs/wiring.md`, one folder down, uses `../`:

```md
![State diagram for the main menu](../images/fsm_diagram.png)
```

The `html` version works the same way if you want to set the size:

```md
<img src="images/fsm_diagram.png" width="300" alt="State diagram for the main menu"/>
```

To add an image to your repository:

1. Create a folder such as `images` (right-click in the VS Code Explorer and choose **New Folder**).
2. Drag the image file into that folder in the Explorer.
3. Add the Markdown line to your README and check it in the preview (**Ctrl+Shift+V**, or **Cmd+Shift+V** on a Mac).
4. Commit and push the image file as well as the README. If you forget the image, the README on GitHub shows a broken link.

Some things that often go wrong:

- the path is relative to the Markdown file, not to the top of the repository
- GitHub treats `FSM.png` and `fsm.png` as different files, even though Windows and macOS do not, so match the case exactly
- spaces in file names break links: use `fsm_diagram.png`, not `FSM diagram.png`
- keep files small: a screenshot is fine, a 50 MB video is not

We can also (over)use `.gifs`, saved in the repository like any other image:

```md
<img src="images/keyboard_cat.gif" alt="A cat in a blue shirt playing an electronic keyboard."/>
```

<img src="{{ "/assets/images/keyboard_cat.gif" | relative_url }}" alt="A cat in a blue shirt playing an electronic keyboard."/>

# Happy Coding!

<table>
<tr>
<td width="50%">
<img src="{{ "/assets/images/cat_typing.gif" | relative_url }}" alt="A black and white cat typing furiously on a laptop."/>
</td>
<td width="50%">
<img src="{{ "/assets/images/dog_typing.gif" | relative_url }}" alt="A golden retriever in a jumper typing on a laptop."/>
</td>
</tr>
</table>
