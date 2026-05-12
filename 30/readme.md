# Day 30

## Task

The Nautilus application development team was working on a git repository /usr/src/kodekloudrepos/beta present on Storage server in Stratos DC. This was just a test repository and one of the developers just pushed a couple of changes for testing, but now they want to clean this repository along with the commit history/work tree, so they want to point back the HEAD and the branch itself to a commit with message add data.txt file. Find below more details:

    In /usr/src/kodekloudrepos/beta git repository, reset the git commit history so that there are only two commits in the commit history i.e initial commit and add data.txt file.

    Also make sure to push your changes

## Solution

ssh into ststor01 and:

```bash
cd /usr/src/kodekloudrepos/beta
git log
```

scrolling down we find this:

```bash
commit 067e016c66e413cd4d8662e3d746d3c8005e1722
Author: Admin <admin@kodekloud.com>
Date:   Mon May 11 10:51:22 2026 +0000

    add data.txt file

commit add87fce0082c621cad5f6841979988870bcd85a
Author: Admin <admin@kodekloud.com>
Date:   Mon May 11 10:51:21 2026 +0000
```

So the commit we want to reset to is `067e016c66e413cd4d8662e3d746d3c8005e1722`

```bash
git reset --hard 067e016c66e413cd4d8662e3d746d3c8005e1722
git push -f
```

## Validation

```bash
git log --oneline
```

It should just show the two commits.

## Insights

`git reset --hard` followed by `git push -f` is kind of scary because you're deleting the actual git history.  I have had to do it a couple times, but it's not something that I really like doing.  There are some other options such as `git revert` that can be used to undo commits without deleting the history, but the task specifically asked to reset the commit history, so I went with that.
