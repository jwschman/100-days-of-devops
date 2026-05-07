# Day 24

## Task

Nautilus developers are actively working on one of the project repositories, /usr/src/kodekloudrepos/beta. Recently, they decided to implement some new features in the application, and they want to maintain those new changes in a separate branch. Below are the requirements that have been shared with the DevOps team:

    On Storage server in Stratos DC create a new branch xfusioncorp_beta from master branch in /usr/src/kodekloudrepos/beta git repo.

    Please do not try to make any changes in the code.

## Solution

```bash
cd /usr/src/kodekloudrepos/beta
git checkout master
git checkout -b xfusioncorp_beta
```

## Validation

```bash
git status
```

It should show that you're on the xfusioncorp_beta branch

## Insights

Another quick one... anyone working with git daily should be able to do this from memory pretty quickly.  The only thing that may have tricked me up was if I didn't switch to the master branch first.  They did try to trick you by starting it out on a different branch, but fortunately I decided to be careful with the instructions and went to master first before branching.
