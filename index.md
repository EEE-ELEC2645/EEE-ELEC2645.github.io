---
title: Home
layout: home
nav_order: 1
description: "ELEC2645 Embedded Systems Project"
---

# Welcome to ELEC2645

![A retro games console. Its LCD screen shows ELEC2645 Embedded Systems Project, the ELEC2645 Bot d[o_0]b and PRESS START, next to a pixel-art Parkinson Building clock tower. Beside the screen are a joystick and two buttons.]({{ "/assets/images/home_banner.svg" | relative_url }})

This is the reference website for the ELEC2645 labs. It covers the C programming and embedded systems topics you need, from your first `printf()` to a games console of your own.

No pointers were harmed in the making of this website. Several were left dangling.

## How to use this site

This site is a reference, not a book: you do not need to read it from start to finish.

- **Use it alongside your lab activity.** When an activity guide uses something new, such as `fgets()`, a struct or a Makefile, look it up here.
- **Search for what you need.** The search box at the top of every page finds functions, keywords and error messages.
- **Run the examples.** Most pages build up in small steps and end with a complete example. Use the copy button on the code block, compile it, run it, then change something and see what happens.
- **Check "Common mistakes" first.** When your build fails or your program does something strange, the "Common mistakes" section of the relevant page lists the problems students meet most often.
- **Minerva has the tasks and deadlines.** The activities, assessment briefs and submission points are on Minerva. This site explains the ideas behind them.

## Which sections go with each unit

| Unit | What you do | Start with |
| --- | --- | --- |
| **Unit 1**: C programming | C recap, arrays and strings, functions and modular code, memory and pointers | [C Basics]({% link docs/C_Basics/c_basics.md %}), [Arrays, Strings and Structs]({% link docs/C_Basics/arrays_strings_structs.md %}), [Functions]({% link docs/functions/functions.md %}), [Files]({% link docs/files/files.md %}), [Building C Projects]({% link docs/projects/projects.md %}), [Pointers]({% link docs/pointers/pointers.md %}), [Debugging]({% link docs/debugging/debugging.md %}) |
| **Unit 2**: command line project | A menu-driven program built as a finite state machine, split into modules and checked with unit tests | [Command Line Basics]({% link docs/menus/menus.md %}), [Finite State Machines]({% link docs/fsm/fsm.md %}), [Multi-file C projects]({% link docs/projects/software_projects.md %}), [Makefiles]({% link docs/projects/makefiles.md %}), [Testing]({% link docs/debugging/testing.md %}), and [Saving and loading records]({% link docs/files/file_records.md %}) for storing data |
| **Unit 3**: STM32 hardware | Programming the NUCLEO-L476RG board: the LCD, joystick, timers, PWM and interrupts | [STM32]({% link docs/stm32/STM32.md %}), [LCD]({% link docs/LCD/LCD.md %}), [Joystick]({% link docs/joystick/joystick.md %}), [Timers]({% link docs/timers/timers.md %}), [PWM]({% link docs/pwm/pwm.md %}), [Interrupts]({% link docs/interrupts/interrupts.md %}) |
| **Unit 4**: group games console | Each group member writes a game, and the group combines them into one console with a menu | The Unit 3 sections, plus [Game characters as state machines]({% link docs/fsm/fsm_games.md %}) and [Working as a team]({% link docs/git/git_teams.md %}) |

Each unit builds on the one before. If a Unit 3 page loses you, the C it relies on is in the Unit 1 and 2 sections.

## Tools

The [Tools]({% link docs/tools.md %}) section covers what you use alongside the C from the first week:

- [Codespaces]({% link docs/codespaces.md %}): opening a Codespace, compiling, running and using tasks
- [Git and GitHub]({% link docs/git/git.md %}): recording your work, branches and pull requests, and sharing a repository with your group
- [Markdown basics]({% link docs/markdown101.md %}): writing READMEs and documentation, with links and images
- [Code styling]({% link docs/code_style.md %}): the layout and naming rules used in this module

## Further reading

All the material needed for assessment will be described on this site, but no one resource can teach you all of `C` or how best to use it in all situations. The following are other places you can look that I have gone through myself:

- [w3schools](https://www.w3schools.com/c/index.php) - covers the basics well and you can run the code online, but the explanations can be a bit limited
- [learn-c](https://www.learn-c.org/) - has short exercises after each topic which I like, though some of it is not so relevant for embedded systems
- [tutorials point C standard library](https://www.tutorialspoint.com/c_standard_library/index.htm) - has detailed examples for each function. Note their online C compiler has problems with using `math.h`. I personally find their [general C course](https://www.tutorialspoint.com/cprogramming/index.htm) a bit too wordy, and some of the explanations are a bit confusing
- [GeeksforGeeks C Programming Language](https://www.geeksforgeeks.org/c-programming-language/) - has a lot of articles on specific topics, but can be a bit all over the place
- [LinkedIn Learning C Courses](https://www.linkedin.com/learning/topics/c) - various video courses on C programming, some free with university access, such as [Complete Guide to C Programming Foundations](https://www.linkedin.com/learning/complete-guide-to-c-programming-foundations?u=2720425)
- [cppreference.com C Language](https://en.cppreference.com/w/c) - comprehensive reference for the C standard library and language features, although it is not the most beginner friendly!
