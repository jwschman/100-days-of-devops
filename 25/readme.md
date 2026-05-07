# Day 25

## Task

The Nautilus application development team has been working on a project repository /opt/media.git. This repo is cloned at /usr/src/kodekloudrepos on storage server in Stratos DC. They recently shared the following requirements with DevOps team:

Create a new branch devops in /usr/src/kodekloudrepos/media repo from master and copy the /tmp/index.html file (present on storage server itself) into the repo. Further, add/commit this file in the new branch and merge back that branch into master branch. Finally, push the changes to the origin for both of the branches.

## Solution

ssh into the storage server and:

```bash
cd /usr/src/kodekloudrepos/media
git checkout master
git checkout -b devops
cp /tmp/index.html .
git add .
git commit -m "add index.html"
git checkout master
git merge devops
git push origin master
git push origin devops
```

## Validation

```bash
git log --oneline
```

## Insights

I may have failed this on the first try because I forgot that the task required me to push to both of the branches, not just main.  Oops.  Once I read the instructions again it was pretty obvious what I missed, so I went back and did it again and got it.

This task was another quite simple one, and I'm glad that git was one of the first things I really learned when I started learning DevOps.

Also, I've done 5 tasks tonight and that seems like plenty for one day.
