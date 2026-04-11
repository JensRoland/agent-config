---
description: Get context on where you left off in a project
allowed-tools: Bash(git log:*), Bash(git diff:*), Bash(git status:*), Bash(git branch:*), Read, Glob, Grep
---

I haven't worked on this project in a while and I can't quite remember where I left off. Please help me regain context:

1. **Project Overview**: Briefly summarize in one or two sentences what this project/repo is about (check README, package.json, or other project files)

2. **Recent Commits**: List and summarize the most recent commits on the current branch (use `git log --oneline -10` or similar)

3. **Uncommitted Changes**: Check for any uncommitted changes with `git status` and `git diff`. If there are any:
   - Summarize what files were modified and how
   - Try to deduce what I was working on based on the changes
   - Note any staged vs unstaged changes separately
