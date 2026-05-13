# Day 34

## Task

The Nautilus application development team was working on a git repository /opt/ecommerce.git which is cloned under /usr/src/kodekloudrepos directory present on Storage server in Stratos DC. The team want to setup a hook on this repository, please find below more details:

    Merge the feature branch into the master branch, but before pushing your changes complete below point.

    Create a post-update hook in this git repository so that whenever any changes are pushed to the master branch, it creates a release tag with name release-2023-06-15, where 2023-06-15 is supposed to be the current date. For example if today is 20th June, 2023 then the release tag must be release-2023-06-20. Make sure you test the hook at least once and create a release tag for today's release.

    Finally remember to push your changes.
    Note: Perform this task using the natasha user, and ensure the repository or existing directory permissions are not altered.

## Solution

ssh into the storage server and:

```bash
cd /opt/ecommerce.git/hooks
vi post-update
```

paste this into the hook:

```bash
#!/bin/bash

if [ "$1" = refs/heads/master ];
then
  echo "creating release tag"
  git tag release-`date '+%Y-%m-%d'` master
fi
```

make the script executable and merge the feature branch and push the changes:

```bash
chmod +x post-update
cd /usr/src/kodekloudrepos/ecommerce
git checkout master
git merge feature --no-ff
git push origin master
```

## Validation

```bash
git fetch --tags
```

## Insights

So I have actually messed around with tags, but as github actions, not post-update hooks, so I did have to do a quick search to figure this one out.  Fortunately I was able to find a guide: <https://medium.com/@bangacts/automating-git-release-tags-with-a-post-update-hook-3266598ca97b>

It told me exactly what I needed to do, and I was able to just copy the very simple script in to generate the tag.  Then all I needed to do was make it executable, merge the change, and push it to master.  
