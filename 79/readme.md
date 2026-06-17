# Day 79

## Task

The Nautilus development team had a meeting with the DevOps team where they discussed automating the deployment of one of their apps using Jenkins (the one in Stratos Datacenter). They want to auto deploy the new changes in case any developer pushes to the repository. As per the requirements mentioned below configure the required Jenkins job.

Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and Adm!n321 password.

Similarly, you can access the Gitea UI using Gitea button. Username and password for Git are sarah and Sarah_pass123. Under user sarah you will find a repository named web that is already cloned on App Server 1 under sarah's home (/home/sarah/web). sarah is a developer who is working on this repository.

1. httpd is already installed and configured on the app server (listening on port 8080). Ensure the httpd service is running on App Server 1 (e.g. start it manually if needed). You can make starting/restarting httpd part of your Jenkins job if you prefer.

2. Create a Jenkins job named nautilus-app-deployment and configure it so that if anyone pushes any new change to the origin repository in master branch, the job should auto build and deploy the latest code on App Server 1 under /var/www/html directory.
Before deployment, ensure that the ownership of the /var/www/html directory is set to user sarah, so that Jenkins can successfully deploy files to that directory.

3. SSH into App Server 1 using sarah user credentials mentioned above. Under sarah user's home (/home/sarah/web) you will find a cloned Git repository named web. Under this repository there is an index.html file, update its content to Welcome to the xFusionCorp Industries, then push the changes to the origin into master branch. This push must trigger your Jenkins job and the latest changes must be deployed on the server, also make sure it deploys the entire repository content not only index.html file.

Click on the App button on the top bar to access the app. Please make sure the required content is loading on the main URL (e.g. http://stlb01:8091) i.e there should not be any sub-directory like http://stlb01:8091/web etc.

Note:
1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also some times Jenkins UI gets stuck when Jenkins service restarts in the back end so in such case please make sure to refresh the UI page.

2. Make sure Jenkins job passes even on repetitive runs as validation may try to build the job multiple times.

3. Deployment related tasks should be done by sudo user on the destination server to avoid any permission issues so make sure to configure your Jenkins job accordingly.

4. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

## Solution

Several of the same steps as previous days so I'm going to skip the `ssh-keygen` and getting the node up in the solution.  We'll continue from after the node is ready:

- Install the git plugin and restart Jenkins
- Create a Freestyle job named `nautilus-app-deployment`:
  - Under General: restrict where this project can be run to label `stapp01`
  - Under Source Code Management: Git
    - Repository URL: the clone URL for the `web` repo
    - Branch: `*/master`
  - Under Build Triggers: check "Trigger builds remotely" and set an authentication token.  I used `deploytoken`.
  - Under Build Steps: Execute shell:

```bash
cp -rf * /var/www/html/
```

- Save the job.

Now generate an API token for the admin user so Gitea can authenticate to Jenkins:

- go to Manage Jenkins > users > admin > security, and under "API Token" click "add new token", generate it, and copy the value.

Then add the webhook in Gitea:

- Open the `web` repo > settings > webhooks > add webhook > Gitea, and set the target URL to the job's build endpoint with the admin credentials included:

```text
https://admin:<API_TOKEN>@<jenkins-url>/job/nautilus-app-deployment/build?token=deploytoken
```

Everything else should be fine.  Click "Test Delivery" at the bottom and make sure it comes back green.

Then we'll update `index.html` and push to trigger the job.  SSH into stapp01 as sarah:

```bash
cd /home/sarah/web
echo "Welcome to the xFusionCorp Industries" > index.html
git add index.html
git commit -m "Update welcome message"
git push origin master
```

The push should start `nautilus-app-deployment` in Jenkins shortly.

## Validation

Watch for the Jenkins job queue after the push.  Make sure it gets a green checkmark, then click the app button and make sure it says "Welcome to the xFusionCorp Industries" and loads at the root URL with no subdirectory.

## Insights

Auto deploy on push wasn't as easy as I hoped it would be.  First I tried using the gitea plugin, but things weren't quite working.  Guides told me to do this, and test deliveries came back OK and manual builds worked, but real pushes never triggered a build.

So I finally just used the "Trigger builds remotely" using a different guide and built the url with the API Token.  It may not have been the cleanest way to handle this, but it's what I got to work.
