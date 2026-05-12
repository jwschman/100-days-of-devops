# Day 33

## Task

Sarah and Max were working on writting some stories which they have pushed to the repository. Max has recently added some new changes and is trying to push them to the repository but he is facing some issues. Below you can find more details:

SSH into storage server using user max and password Max_pass123. Under /home/max you will find the story-blog repository. Try to push the changes to the origin repo and fix the issues. The story-index.txt must have titles for all 4 stories. Additionally, there is a typo in The Lion and the Mooose line where Mooose should be Mouse.

Click on the Gitea UI button on the top bar. You should be able to access the Gitea page. You can login to Gitea server from UI using username sarah and password Sarah_pass123 or username max and password Max_pass123.

Note: For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

## Solution

ssh into storage server with max and:

```bash
cd story-blog
git pull origin master
```

Then we need to edit the story-index.txt file following the instructions:

```bash
vi story-index.txt
```

After making the changes we can add, commit, and push the changes:

```bash
git add story-index.txt 
git commit -m "Resolve merge conflict in story-index.txt and fix typo in lion-and-mouse.txt"
git push origin master
```

## Validation

The push shouldn't give any errors, and we should be able to check the Gitea UI to see the changes in the repo.  Or just do `git log` to see the latest commit.

## Insights

I'm not really sure why the task specified the gitea UI, since it was entirely doable from the command line.  It may have just been another way to check our work, but I also remember during the introduction to the course that there would be some incomplete or misleading instructions, reflecting real-world scenarios, so maybe that was the case here.

Either way, it was a pretty straightforward task and good to practice some merge conflicts.
