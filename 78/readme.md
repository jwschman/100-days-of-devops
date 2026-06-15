# Day 78

## Task

The development team of xFusionCorp Industries is working on to develop a new static website and they are planning to deploy the same on Nautilus App Server using Jenkins pipeline. They have shared their requirements with the DevOps team and accordingly we need to create a Jenkins pipeline job. Please find below more details about the task:

Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

Similarly, click on the Gitea button on the top bar to access the Gitea UI. Login using username sarah and password Sarah_pass123. There under user sarah you will find a repository named web_app that is already cloned on App Server 1 under /var/www/html. sarah is a developer who is working on this repository.

    Add a slave node named App Server 1. It should be labeled as stapp01 and its remote root directory should be /home/sarah/jenkins_agent (the repository is cloned under /var/www/html).

    We have already cloned repository on App Server 1 under /var/www/html.

    Apache is already installed on the app server and is running on port 8080.

    Create a Jenkins pipeline job named xfusion-webapp-job (it must not be a Multibranch pipeline) and configure it to:

        Add a string parameter named BRANCH.

        It should conditionally deploy the code from web_app repository under /var/www/html on App Server 1, as this is the document root of the app server. The pipeline should have a single stage named Deploy ( which is case sensitive ) to accomplish the deployment.

        The pipeline should be conditional, if the value master is passed to the BRANCH parameter then it must deploy the master branch, on the other hand if the value feature is passed to the BRANCH parameter then it must deploy the feature branch.

LB server is already configured. You should be able to see the latest changes you made by clicking on the App button. Please make sure the required content is loading on the main URL https://<LBR-URL> i.e there should not be a sub-directory like https://<LBR-URL>/web_app etc.

Note:

    You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.

    For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.


## Solution

This task is just like day 77, only we need to add the conditional pipeline code.  So we mostly just copy the steps:

- Get SSH access from Jenkins to stapp01

```bash
ssh-keygen
ssh-copy-id sarah@stapp01
cat ~/.ssh/id_ed25519
```

- Add the SSH Build Agent plugin then add "SSH Username with private key" credentials for `sarah` using the generated key.
- Add the slave node
  - Name: `App Server 1`, label: `stapp01`
  - Remote root directory: `/home/sarah/jenkins_agent`
  - Launch method: Launch agents via SSH - Host: `stapp01`, Credentials: sarah

>If the agent fails to connect, install Java on stapp01:

```bash
sudo yum install -y java-17-openjdk
```

Make sure it says `Agent successfully connected and online` in the node log.

- Install the Pipeline and Git plugins and restart Jenkins
- Create a Pipeline job named `xfusion-webapp-job`
- Under the Pipeline section use this script, replacing the Gitea URL with the HTTPS clone URL copied from the `web_app` repo in the Gitea UI:

```groovy
pipeline {
    agent { label 'stapp01' }
    parameters {
        string(name: 'BRANCH', defaultValue: 'master', description: 'Branch to deploy')
    }
    stages {
        stage('Deploy') {
            when {
                anyOf {
                    expression { params.BRANCH == 'master' }
                    expression { params.BRANCH == 'feature' }
                }
            }
            steps {
                git url: 'https://3000-port-7fytstj2ha26jjgu.labs.kodekloud.com/sarah/web_app.git',
                    branch: "${params.BRANCH}"
                sh 'cp -rf * /var/www/html/'
            }
        }
    }
}
```

Save, then click "Build with Parameters" and use `master` or `feature` as the `BRANCH` value.

## Validation

Click "Build with Parameters" and set `BRANCH` to `master`, and confirm a green checkmark on the build. Then repeat with `BRANCH` set to `feature`. Click the App button and confirm the site loads at the root URL with no subdirectory.

## Insights

This was essentially Day 77 with one extra step... the conditional branch parameter.  Again I had to check a guide and found the `when { anyOf { expression ... } }` to be a simple way to handle it.

I was also able to declare the parameters inside the pipeline script so they didn't need to be set inside the Jenkins gui.
