---
author: ["Maza Fard"]
date: '2025-11-22'
description: A bit of a rant about VS Code and a tip on separating untracked files from changes.
tags: ["vscode", "editor", "git", "settings"]
categories: ["technology", "tools"]
series: ["dev tools"]
ShowToc: true
weight: 1
title: Working with VS Code (Reluctantly!)
---

## Why is VS Code so clunky?

Seriously, why is VS Code so tired and heavy? It feels like opening a whole web browser just to write a few lines of code. The more plugins you add, the slower it gets. I really don't get the hype.

## Why is everyone using it?

What annoys me even more is that it's becoming the standard everywhere. Every tutorial, every team, everyone is on VS Code. I'm honestly not happy about it taking over the world when there are faster, lighter editors out there. But hey, we can't fight the tide forever.

## Since we're stuck with it...

Since we are forced to use it every now and then, we might as well learn how to make it behave. Today, let's talk about separating your staged/modified changes from your untracked files.

By default, VS Code jumbles everything together, which is messy.

![VS Code Source Control panel showing staged, modified, and untracked files all mixed together in a single list.](/images/vscode/vscode-git-mixed-changes.png)

To make it show tracked and untracked changes separately (like a civilized tool), do this:

1.  Press `Ctrl + Shift + P` (or `Cmd + Shift + P` on Mac).
2.  Type and select: `Preferences: Open Settings (UI)`.
3.  Search for: `Git: Untracked Changes`.
4.  Set it to `separate`.

```json
"git.untrackedChanges": "separate"
```

Now, if you check your Source Control tab, you'll see "Untracked Changes" in their own separate list, distinct from your modified files. Much better!

![VS Code Source Control panel showing a separate section for Untracked Changes, distinct from Changes.](/images/vscode/vscode-git-separate-untracked.png)
