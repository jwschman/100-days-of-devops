# Day 27

## Task

The Nautilus application development team was working on a git repository /usr/src/kodekloudrepos/games present on Storage server in Stratos DC. However, they reported an issue with the recent commits being pushed to this repo. They have asked the DevOps team to revert repo HEAD to last commit. Below are more details about the task:

    In /usr/src/kodekloudrepos/games git repository, revert the latest commit ( HEAD ) to the previous commit (JFYI the previous commit hash should be with initial commit message ).

    Use revert games message (please use all small letters for commit message) for the new revert commit.

## Solution

ssh into the storage server and check the git history:

```bash
cd /usr/src/kodekloudrepos/games
git log --oneline
```

```bash
a5533c1 (HEAD -> master, origin/master) add data.txt file
90b09c1 initial commit
```

Then just revert to the previous commit:

```bash
git revert HEAD
```

Remove the default comments and set it to just `revert games`

```
git push origin master
```

## Validation

```bash
git log --oneline
```

## Insights

A revert is a new commit that undoes the changes from a previous commit.  The kind of weird thing is that you can't just pass `-m "message"` to the `git revert` command, you have to edit the default message and change it to whatever you want, in this case `revert games`.

I don't think I've ever actually had to do a revert before, but it was pretty straightforward.  It didn't mention it in the task, but I did a git push at the end just to be sure it was applied.
