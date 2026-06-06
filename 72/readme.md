# Day 72

## Task

A new DevOps Engineer has joined the team and he will be assigned some Jenkins related tasks. Before that, the team wanted to test a simple parameterized job to understand basic functionality of parameterized builds. He is given a simple parameterized job to build in Jenkins. Please find more details below:

Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

1. Create a parameterized job which should be named as parameterized-job

2. Add a string parameter named Stage; its default value should be Build.

3. Add a choice parameter named env; its choices should be Development, Staging and Production.

4. Configure job to execute a shell command, which should echo both parameter values (you are passing in the job).

5. Build the Jenkins job at least once with choice parameter value Development to make sure it passes.

Note:

1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.

2. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

## Solution

Sure looks a lot like the last task.  Let's get to it:

- Click create a job, name it parameterized-job, click freestyle project
- Click the "This project is paramaterized" and add a string parameter named stage with default value Build
- Then add a Choice Paramater, set the name to env, and the choices should be Development, Staging, and Production... one choice per line
- Add a build step, set it to execute shell
- Inside the Command, set it to:

```bash
echo "${Stage}"
echo "${env}"
```

- Click save and then click Build with Paramaters
- Then just click build

## Validation

Click on the build number, then click console output.  It should look like this:

```bash
Started by user admin
Running as SYSTEM
Building in workspace /var/lib/jenkins/workspace/parameterized-job
[parameterized-job] $ /bin/sh -xe /tmp/jenkins2657272697640026838.sh
+ echo Build
Build
+ echo Development
Development
Finished: SUCCESS
```

## Insights

This just kind of seemed like a simpler version of the previous task, only it also wanted to set up a Choice Paramater.  Nothing really any different than the previous one.  I was a bit worried because it didn't say how it wanted the `echo` to be formatted, but doing it on two lines worked no problem.
