---
title: Codespaces
nav_order: 98
layout: default
---

# GitHub Codespaces

GitHub Codespaces provides a ready-to-use programming environment in your web browser. It looks and behaves like Visual Studio Code, but the compiler and other tools run on a computer provided by GitHub.

## Opening a Codespace

From your GitHub Classroom repository:

1. Select the green **Code** button.
2. Select the **Codespaces** tab.
3. Select **Create codespace on main**.
4. Wait for the Codespace to open.

Your files appear on the left, the editor is in the centre, and the terminal normally appears at the bottom.

If the terminal is hidden, select **Terminal > New Terminal**.

## Files in an activity

A typical activity repository contains:

```text
.devcontainer/    Codespace configuration
.vscode/          Run, build and debug configuration
.gitignore        Files that Git should not track
Makefile          Instructions for compiling and testing
README.md         Activity instructions
main.c            C source code for you to edit
test.sh           Test script used by ELEC2645 Bot
```

The most important files for you are the `.c` source files and the activity instructions in the `.md` files.

## Compiling and running C code

C source code must be compiled before it can run.

For example:

```bash
gcc main.c -o main.out
./main.out
```

The first command uses GCC to compile `main.c` and create `main.out`. The second command runs the compiled program.

> **Important:** Saving a change to a `.c` file does not update the compiled program automatically. Compile it again before running it if you want to see your changes.

Many activities provide a Makefile, so you can compile and test the program with:

```bash
make test
```

Follow the README for the activity to find the correct command, such as `make test1` or `make test2`.

## Helpful terminal shortcuts

The terminal remembers commands and can help you type filenames:

- Press **Up Arrow** to return to an earlier command.
- Press **Down Arrow** to move forwards through your command history.
- Press **Tab** to autocomplete a command or filename.
- Press **Ctrl+C** to stop a program that is still running, such as an accidental infinite loop.
- Use `clear` to clear the terminal display.

For example, type:

```text
gcc act
```

and press **Tab**. If there is only one matching filename, the terminal will complete it for you.

## Tasks

Some repositories contain predefined VS Code Tasks.

Select **Terminal > Run Task**, then choose an activity to build, run or test. The task runs the appropriate Makefile command for you.

Using tasks is optional. The same commands can still be entered directly in the terminal.

## Run and Debug

The **Run and Debug** panel can run a program one line at a time and show the current values of its variables.

To try it:

1. Open **Run and Debug** from the left-hand toolbar.
2. Select the activity you are working on.
3. Click beside a line number to add a breakpoint.
4. Press `F5` or select the green play button.
5. Use **Step Over** to move through the program one line at a time.

The debugger first uses the Makefile to compile the correct source files.

> The debugger can only start after the program compiles successfully. Fix any compiler errors first.

Run and Debug is optional for now. We will look at debugging properly later in the course.

## Saving your work

Changes made inside a Codespace are not submitted until you commit and sync them:

1. Open **Source Control**.
2. Stage your changed files using `+`.
3. Enter a meaningful commit message.
4. Select **Commit**.
5. Select **Sync Changes**.

Check your repository on GitHub before finishing to make sure your latest work has been uploaded.