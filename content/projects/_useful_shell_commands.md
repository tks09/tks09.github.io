---
title: "Useful shell commands"
date: 2022-11-01T10:23:54+01:00
author: Tobi
layout: single
categories:
    - Shell
    - Bash
    - Git
series:
    - Tech
draft: false
---

## Git

### 1. Change the author for all git commits since root of a branch
*remember to change the user name and email before!*

```bash 
git rebase --interactive --exec "git commit --amend --reset-author" --root
```

