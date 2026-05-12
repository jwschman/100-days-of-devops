# Day 31

## Task

The Nautilus application development team was working on a git repository /usr/src/kodekloudrepos/news present on Storage server in Stratos DC. One of the developers stashed some in-progress changes in this repository, but now they want to restore some of the stashed changes. Find below more details to accomplish this task:

Look for the stashed changes under /usr/src/kodekloudrepos/news git repository, and restore the stash with stash@{1} identifier. Further, commit and push your changes to the origin.

## Solution

ssh into ststor01 again and check things out:

```bash
cd /usr/src/kodekloudrepos/news
git stash list
```

This is the output I got:

```bash
stash@{0}: WIP on master: a5d8243 initial commit
stash@{1}: WIP on master: a5d8243 initial commit
```

So just apply (or pop) the stash, commit, and push:

```bash
git commit -m "add welcome.txt"
git push origin HEAD
```

## Validation

```bash
git log
```

## Insights

I don't have a ton of experience actually using stash, but I know how to use it and what it's for.  It seems like a pretty useful way to save work in progress without committing it to the repository and messing up the commit history.  I can also see how it would be useful if you need to switch branches and you have uncommitted changes that you don't want to lose.
