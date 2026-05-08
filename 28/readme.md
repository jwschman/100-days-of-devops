# Day 28

## Task

The Nautilus application development team has been working on a project repository /opt/official.git. This repo is cloned at /usr/src/kodekloudrepos on storage server in Stratos DC. They recently shared the following requirements with the DevOps team:

There are two branches in this repository, master and feature. One of the developers is working on the feature branch and their work is still in progress, however they want to merge one of the commits from the feature branch to the master branch, the message for the commit that needs to be merged into master is Update info.txt. Accomplish this task for them, also remember to push your changes eventually.

## Solution

ssh into the storage server and:

```bash
cd /usr/src/kodekloudrepos/apps
git status
git log --oneline
```

That will show that we're on the feature branch, and also the commit history which has the commit we want to cherry pick:

```bash
deb1d82 (HEAD -> feature, origin/feature) Update welcome.txt
caa2721 Update info.txt
e13d08b (origin/master, master) Add welcome.txt
3950e45 initial commit
```

We need to switch to master and merge:

```bash
git checkout master
```

The commit we want to merge is caa2721, so we can use the cherry-pick command to merge it into master:

```bash
git cherry-pick caa2721
git push origin master
``` 

## Validation

```bash
git log --oneline
```

It should have the same commit message as the one in the feature branch:

## Insights

We're definitely getting into some more obscure (to me) git commands.  I didn't even know that cherry-pick was a feature, but I can see how it would be useful to apply changes from existing commits to a branch.
