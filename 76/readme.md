# Day 76

## Task

The xFusionCorp Industries has recruited some new developers. There are already some existing jobs on Jenkins and two of these new developers need permissions to access those jobs. The development team has already shared those requirements with the DevOps team, so as per details mentioned below grant required permissions to the developers.

Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

    There is an existing Jenkins job named Packages, there are also two existing Jenkins users named sam with password sam@pass12345 and rohan with password rohan@pass12345.

    Grant permissions to these users to access Packages job as per details mentioned below:

    a.) Make sure to select Inherit permissions from parent ACL under inheritance strategy for granting permissions to these users.

    b.) Grant mentioned permissions to sam user : build, configure and read.

    c.) Grant mentioned permissions to rohan user : build, cancel, configure, read, update and tag.

Note:

    Please do not modify/alter any other existing job configuration.

    You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.

    For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

## Solution

So I'm going to go back to using the Project-based Matrix Authorization Strategy plugin to give per-job permissions:

- Go to Manage Jenkins > Plugins, install **Matrix Authorization Strategy** plugin and restart
- Go to Manage Jenkins > Security, set Authorization to **Project-based Matrix Authorization Strategy** and save
- Then open the **Packages** job > Configure > Click **Enable project-based security**
- Make sure inheritance strategy is set to **Inherit permissions from parent ACL**
- Add user `sam` and grant: build, configure and read
- Add user `rohan` and grant: build, cancel, configure, read, update and tag
- Save

## Validation

Log in as sam and verify you can see and build the Packages job but not access admin settings. Repeat for rohan and confirm the extra permissions (cancel, update, tag) are available.

## Insights

Since we already used the **Matrix Authorization Strategy** plugin earlier this was pretty easy to accomplish.  The users were already made, so all we had to do was install the plugin, set the job to use it, and then add the users and their designated permissions.

This was actually the simplest task tonight since I didn't have to mess with any ssh keys, just install a plugin and configure some permissions in a GUI.
