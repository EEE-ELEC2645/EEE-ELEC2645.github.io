---
title: Working as a team
parent: Git and GitHub
grand_parent: Tools
nav_order: 3
layout: default
---

# Working as a team

<details markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

In the Unit 4 group project, each member writes a game and the group combines them into one games console with a menu. GitHub Classroom creates one repository for the whole group, and everyone pushes to it. This page covers how to share that repository without treading on each other's work, and what to do when Git says two changes clash.

It builds on [The everyday workflow]({% link docs/git/git_workflow.md %}) and [Branches and pull requests]({% link docs/git/git_branches.md %}). The output shown is from a real run, with the lines naming the repository's address left out.

## Agree who edits which files

Git can combine changes to different files, and to different parts of the same file, without any help. Problems start when two people change the same lines. The simplest way to avoid that is to agree, before anyone writes code, who edits what.

The Unit 4 menu template is laid out for this. Each game has its own folder, and the code everyone uses is kept separate:

| Files | Who edits them |
| --- | --- |
| `game_1/`, `game_2/`, `game_3/` | the owner of that game, freely |
| `shared/` (the menu and input handling) | agree first, then one person makes the change |
| `Core/Src/main.c`, `CMakeLists.txt` | agree first, then one person makes the change |

Adding a new `.c` file to your game means adding a line to `CMakeLists.txt`. Tell the group, and change only your own lines.

Do not reformat, rename or tidy up code in files you do not own, even if it would be better. A one-line fix becomes a whole-file conflict for everyone else.

## A routine that avoids trouble

Each time you sit down to work:

1. `git switch main` and `git pull`, to start from everyone's latest work.
2. `git switch -c your-feature` to make a branch.
3. Edit, build and test. Commit small working steps as you go.
4. `git push -u origin your-feature`, then open a pull request.
5. Check the **Files changed** tab, then merge. If a teammate has time to look first, even better.
6. Tell the group, so they know to pull.

Pull requests are recommended but not required: you can also push straight to `main`. Either way, pull before you start and push when you finish.

Talk to each other. Most merge problems are really "we both decided to fix the menu on the same evening" problems.

## Set up `git pull` once

The first time someone else has pushed while you also have new commits, a plain `git pull` stops and asks how to combine them:

```text
hint: You have divergent branches and need to specify how to reconcile them.
hint: You can do so by running one of the following commands sometime before
hint: your next pull:
hint:
hint:   git config pull.rebase false  # merge
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint:
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
fatal: Need to specify how to reconcile divergent branches.
```

Choose merge, which is what this page uses, once in each copy of the repository:

```bash
git config pull.rebase false
```

Use `git config --global pull.rebase false` instead to set it for every repository on that computer or Codespace.

## When a push is rejected

If a teammate pushed while you were working, your push is refused:

```text
 ! [rejected]        main -> main (fetch first)
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. This is usually caused by another repository pushing to
hint: the same ref. If you want to integrate the remote changes, use
hint: 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

Nothing is wrong: Git will not let you overwrite their work. Run `git pull` to bring their commits in, build and test, then `git push` again.

Never use `git push --force` on a shared repository. It replaces what is on GitHub with your copy and throws away your teammates' commits.

## Merge conflicts

A merge conflict happens when two people change the same lines and Git cannot tell which version to keep. Here, two students both edit a simplified `shared/menu.c`:

```c
static const char *game_names[] = { "Game 1", "Game 2", "Game 3" };
```

Student A changes `"Game 1"` to `"Lava Hop"` and pushes. Student B, on the same line, changes `"Game 2"` to `"Cave Diver"`, commits, and has their push rejected. Student B pulls:

```bash
git pull
```

```text
Auto-merging shared/menu.c
CONFLICT (content): Merge conflict in shared/menu.c
Automatic merge failed; fix conflicts and then commit the result.
```

`git status` lists the file under "Unmerged paths":

```text
On branch main
Your branch and 'origin/main' have diverged,
and have 1 and 1 different commits each, respectively.
  (use "git pull" if you want to integrate the remote branch with yours)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
	both modified:   shared/menu.c

no changes added to commit (use "git add" and/or "git commit -a")
```

### Reading the conflict markers

Git has written both versions into the file, between markers:

```c
#include <stdio.h>

<<<<<<< HEAD
static const char *game_names[] = { "Game 1", "Cave Diver", "Game 3" };
=======
static const char *game_names[] = { "Lava Hop", "Game 2", "Game 3" };
>>>>>>> 5dd75a71c10e71f04ebf0f4c2ad30eeb31a4648b

void menu_print(void)
{
    for (int i = 0; i < 3; i++) {
        printf("%d. %s\n", i + 1, game_names[i]);
    }
}
```

- between `<<<<<<< HEAD` and `=======` is your version (Student B's)
- between `=======` and `>>>>>>>` is the incoming version from GitHub (Student A's commit)

The file will not compile like this. Until the conflict is fixed, nobody should build from it.

### Fixing it

Edit the file so it is what the group actually wants, and delete all three marker lines. Here both names should stay:

```c
static const char *game_names[] = { "Lava Hop", "Cave Diver", "Game 3" };
```

VS Code shows buttons above each conflict: **Accept Current Change**, **Accept Incoming Change** and **Accept Both Changes**. They are a good start, but think before clicking. **Accept Both Changes** keeps both lines here, which does not compile:

```text
menu.c:4:20: error: redefinition of ‘game_names’
    4 | static const char *game_names[] = { "Lava Hop", "Game 2", "Game 3" };
      |                    ^~~~~~~~~~
menu.c:3:20: note: previous definition of ‘game_names’ with type ‘const char *[3]’
    3 | static const char *game_names[] = { "Game 1", "Cave Diver", "Game 3" };
      |                    ^~~~~~~~~~
```

Whichever way you edit it, build and test before going on. Then mark the file as fixed and finish the merge:

```bash
git add shared/menu.c
git commit -m "Merge main, keeping both game names"
git push
```

```text
[main b9bfa37] Merge main, keeping both game names
   5dd75a7..b9bfa37  main -> main
```

`git log --oneline --graph` shows the two changes coming together:

```text
*   b9bfa37 Merge main, keeping both game names
|\  
| * 5dd75a7 Name game 1 Lava Hop
* | ef70216 Name game 2 Cave Diver
|/  
* 76b32fe Add menu
```

If a merge goes badly and you want to start again, `git merge --abort` puts everything back as it was before the pull.

### Conflicts in a pull request

If `main` has changed in a way that conflicts with your branch, GitHub says the pull request "has conflicts that must be resolved". Fix it in your own copy: on your branch, run `git pull origin main` to merge the latest `main` into it, fix the conflicts as above, then `git push`. The pull request updates itself.

## Complete example

Student B's whole session, from the rejected push to the fixed merge:

```bash
git push                              # rejected: Student A pushed first
git config pull.rebase false          # only needed once
git pull                              # conflict in shared/menu.c
git status
# ... edit shared/menu.c: keep both names, delete the markers, build and test ...
git add shared/menu.c
git commit -m "Merge main, keeping both game names"
git push
```

This produces (the lines naming the repository, the `git status` output and the push hints left out):

```text
 ! [rejected]        main -> main (fetch first)
Auto-merging shared/menu.c
CONFLICT (content): Merge conflict in shared/menu.c
Automatic merge failed; fix conflicts and then commit the result.
[main b9bfa37] Merge main, keeping both game names
   5dd75a7..b9bfa37  main -> main
```

## Common mistakes

### Everyone editing the menu

If every member adds their game to the menu on the same day, everyone gets a conflict. Agree that one person makes changes to `shared/` and `main.c`, or take turns.

### Committing the build folder

The `build/` folder is recreated every time you build, and different on every computer. If it is committed, every build causes conflicts. The template's `.gitignore` lists it; check `git status` before `git add .` and do not remove that line.

### Merging everything the week before the deadline

Combining three games for the first time at the end is when everything goes wrong at once. Merge into `main` often, so the console always builds with everyone's latest working code.

### Deleting the markers without testing

A file with no conflict markers can still be wrong. Build and test after every merge, before you push.

## Summary

The main points to remember are:

- agree who edits which files; shared files are changed by one person after agreeing
- pull before you start, push when you finish, and tell the group
- run `git config pull.rebase false` once in each copy of the repository
- a rejected push means someone pushed first: pull, build, test, push again
- fix a conflict by editing the file to what you want, deleting the markers, building, testing, then `git add` and `git commit`
- never `git push --force` on a shared repository
