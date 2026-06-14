# Day 77

## Task

The development team of xFusionCorp Industries is working on to develop a new static website and they are planning to deploy the same on Nautilus App Server using Jenkins pipeline. They have shared their requirements with the DevOps team and accordingly we need to create a Jenkins pipeline job. Please find below more details about the task:

Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

Similarly, click on the Gitea button on the top bar to access the Gitea UI. Login using username sarah and password Sarah_pass123. There under user sarah you will find a repository named web_app that is already cloned on App Server 1 under /var/www/html. sarah is a developer who is working on this repository.

    Add a slave node named App Server 1. It should be labeled as stapp01 and its remote root directory should be /home/sarah/jenkins_agent (the repository is cloned under /var/www/html; the agent uses a separate directory so it does not pollute the repo).

    We have already cloned repository on App Server 1 under /var/www/html.

    Apache is already installed on the app server and is running on port 8080.

    Create a Jenkins pipeline job named datacenter-webapp-job (it must not be a Multibranch pipeline) and configure it to:

        Deploy the code from web_app repository under /var/www/html on App Server 1, as this is the document root of the app server. The pipeline should have a single stage named Deploy ( which is case sensitive ) to accomplish the deployment.

LB server is already configured. You should be able to see the latest changes you made by clicking on the App button. Please make sure the required content is loading on the main URL https://<LBR-URL> i.e there should not be a sub-directory like https://<LBR-URL>/web_app etc.

Note:

    You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.

    For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

## Solution

First set up SSH access from the Jenkins server to stapp01 as sarah:

```bash
ssh-keygen
ssh-copy-id sarah@stapp01
cat ~/.ssh/id_ed25519
```

First we need to add the SSH Build Agent plugin again.  Then go to credentials and add **SSH Username with private key** credentials for sarah including the key that we just generated.

  Then we can add the new node:

- Name: `App Server 1`, label: `stapp01`
- Remote root directory: `/home/sarah/jenkins_agent`
- Launch method: Launch agents via SSH:
  - Host: `stapp01`
  - Credentials: sarah

>I'm getting the same error as before, so we need to install java 17 on stapp01 again:

```bash
sudo yum install -y java-17-openjdk
```

We should see `Agent successfully connected and online` in the log to know it's connected.

Then get the Gitea repo URL by opening the `web_app` repo in the Gitea UI and copying the HTTPS clone URL.  For me it's `https://3000-port-jxsxl352zsuez2k5.labs.kodekloud.com/sarah/web_app.git`

Back in Jenkins, to create a Pipeline job we first need to install the Pipeline and Git plugins and restart.

Then create a Pipeline job named `datacenter-webapp-job`. Under Pipeline add this to the script:

```groovy
pipeline {
    agent { label 'stapp01' }
    stages {
        stage('Deploy') {
            steps {
                git url: 'https://3000-port-jxsxl352zsuez2k5.labs.kodekloud.com/sarah/web_app.git',
                    branch: 'master'
                sh 'cp -rf * /var/www/html/'
            }
        }
    }
}
```

Save and click Build Now.  If everything is set up correctly we should get no errors and a nice green checkmark.

## Validation

Click the App button and confirm the site shows "Welcome to xFusionCorp Industries!" at the root URL with no subdirectory.

## Insights

Lots of things to set up here, but it all made sense now that I've had a little bit of experience with Jenkins.

The thing I didn't have any experience with was pipeline jobs, so I had to look up a guide on setting up pipeline script using git, but once I had that we were mostly good to go.

I did have a brief problem with the guide using `main` for the branch, but the actual Gitea branch was `master` so the first build attempt failed.  Once I set the branch to `master` things built fine and the site was up.
