---
title: "Change git branch (master to main)"
date: 2021-03-17T13:59:39-07:00
tags: ['git']
draft: true
---

Here's another thing I've googled enough times to just put it somewhere I know.

First, rename your local branch:

```git
❯ git branch -m master main
```

Then you can check that it worked:

```
❯ git status
On branch main
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
```

If you don't have dependencies that might get screwed up by simply renaming your master branch, you can just navigate to your remote directory and change the name (if you use github, from the repo's home page, click Settings > Branches and then click the edit button that looks like a little pencil). Then just type in your new branch name (main). 

Next update your local repository's remote to reference the renamed branch (if your ):

```
❯ git fetch origin
❯ git branch -u origin main
```

That should do it. 

Alternatively, if you don't want to rename your remote, you can go straight from the first step (renaming your local repo) and create a new remote branch and set it as your default branch:

```
❯ git push -u origin main
```

Once you have run this command, go to the remote repo and change (Settings > Branches, then click the two opposing horizontal arrows to change your default branch).