---
layout: post
title: Useful git features
author: tuan
categories: [tutorial]
image: assets/images/2020/computer.jpeg
tags: []
---

Useful git features that may help the flow while programming and working in a team.

### Stashing unfinished changes

I use `git stash` quite regularly especially when there is a change in priority or urgent changes is required. 

`git stash --include-untracked` allows us to save new files that have been created that is not apart of the project yet.

This is mostly used when I am experimenting changes and not ready to make a commit without losing the work.

`git stash apply` is used more often over `git stash pop` because it does not remove the stash. This is used in the case I forget and have to remove the changes again.

### Git commit with description and template

I use `git commit` over `git commit -m 'Info..'` because it allows us to add a description to the commit.

Additionally, a template can be used to set a standard on what is required when performming a change.

### Git hooks

Forcing a command to run when we run `git push` can be done via git hooks.

The way this works is by creating a file in the `.git/hooks/` folder and inserting a chosen command to run before the push to remote occurs.

More information on what is possible can be found here [https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks).

An example can be done for this specific project. Because we use Jekyll and Github Pages, we need to rebuild the project each time we push to Github.

We can add in a `pre-commit` hook which runs `jekyll build` and `rm -r docs && mv _site docs` to refresh the folder.

### Git Flow

Git Flow is a workflow designed to manage large projects with release cycles. Its useful as it creates a consistent structure/framework for the team to work with, without too many obstacles. 

It builds on the feature branch workflow, extending it for releases that occur regularly.

An example to start and finish a release:
- `git flow release start 1.0.0`
- `git flow release finish 1.0.0`

An example to start and finish a feature branch:
- `git flow feature start fix-new-bug`
- `git flow feature finish fix-new-bug`

More information can be found here [https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).

### Removing untracked files and folders

`git clean -fd` removes any local project files that are new and have not been commited to the project. This command is useful to run along with `git stash` when switching priorities.