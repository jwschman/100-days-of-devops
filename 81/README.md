# Day 81

## Task

The development team of xFusionCorp Industries is working on to develop a new static website and they are planning to deploy the same on Nautilus App Server using Jenkins pipeline. They have shared their requirements with the DevOps team and accordingly we need to create a Jenkins pipeline job. Please find below more details about the task:

Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

Similarly, click on the Gitea button on the top bar to access the Gitea UI. Login using username sarah and password Sarah_pass123.

There is a repository named sarah/web in Gitea that is already cloned on App Server 1 under /var/www/html directory.

    Update the content of the file index.html under the same repository to Welcome to xFusionCorp Industries and push the changes to the origin into the master branch.

    Apache is already installed on the app server and is running on port 8080.

    Add App Server 1 as a Jenkins agent (slave) node: name App Server 1, label stapp01, remote root directory /home/sarah/jenkins_agent, launch via SSH with host stapp01 and credentials for user sarah. Install java-17-openjdk on App Server 1 if needed.

    Create a Jenkins pipeline job named deploy-job (it must not be a Multibranch pipeline job) and pipeline should have two stages Deploy and Test ( names are case sensitive ). Configure these stages as per details mentioned below.

    a. The Deploy stage should deploy the code from web repository under /var/www/html on App Server 1, as this is the document root of the app server.

    b. The pipeline should run on the App Server 1 node (e.g. use label stapp01).

    c. The Test stage should just test if the app is working fine and website is accessible. Its up to you how you design this stage to test it out, you can simply add a curl command as well to run a curl against the LBR URL (http://stlb01:8091) to see if the website is working or not. Make sure this stage fails in case the website/app is not working or if the Deploy stage fails.

Click on the App button on the top bar to see the latest changes you deployed. Please make sure the required content is loading on the main URL http://stlb01:8091 i.e there should not be a sub-directory like http://stlb01:8091/web etc.

Note:

    You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.

    For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

## Solution

Once again set everything up for the agent to be running on the node.  Once it's successfully connected we'll get things going.

The `web` repo is already cloned at `/var/www/html`, and the deploy step copies files into that directory, so it needs to be owned by sarah.  SSH into stapp01 and run:

```bash
sudo chown -R sarah:sarah /var/www/html
```

Now update `index.html` and push it to master.  SSH into stapp01 as sarah and edit the file inside the existing clone:

```bash
cd /var/www/html
echo "Welcome to xFusionCorp Industries" > index.html
git add index.html
git commit -m "Update welcome message"
git push origin master
```

Back in Jenkins:

- Install the Pipeline and Git plugins and restart
- Create a Pipeline job named `deploy-job`
-  Under the Pipeline section: replacing the Gitea URL with the HTTPS clone URL copied from the `web` repo in the Gitea UI:

```groovy
pipeline {
    agent { label 'stapp01' }
    stages {
        stage('Deploy') {
            steps {
                git url: 'https://3000-port-ilmuib72h7hvrq2x.labs.kodekloud.com/sarah/web.git',
                    branch: 'master'
                sh 'cp -rf * /var/www/html/'
            }
        }
        stage('Test') {
            steps {
                sh 'curl -fs http://stlb01:8091 -o /dev/null'
            }
        }
    }
}
```

>Make sure to replace the git url with the HTTPS clone url from the `web` repo in the Gitea UI.

Save and click "Build Now".

The `Deploy` stage will check out the repo into the agent workspace and copies it into the document root `/var/www/html` on stapp01.  The `Test` stage then curls the load balancer URL with `-f`, which makes curl exit non-zero on any problem.  And because `Test` runs in a later stage, a failed `Deploy` will stop the pipeline before it ever gets there.

## Validation

Click "Build Now" and confirm both `Deploy` and `Test` stages complete without issue.  Then click the App button and verify it loads at the right URL.

The task also says it might be a good idea to see if the `Test` stage actually fails when it should.  I checked it two ways:

- Ran `sudo systemctl stop httpd` on stapp01 and rebuilt.  `curl -f` got no good response so `Test` failed.
- After restarting httpd (`sudo systemctl start httpd`) I broke the git URL in the `Deploy` stage and rebuilt so `Deploy` failed and the pipeline never even made it to `Test`.

After I made sure it broke correctly (that sounds weird) I made sure the mistakes were corrected and ran it again just to make sure it was still working.

## Insights

At this point most of these Jenkins tasks are muscle memory.  Add the ssh keys to `stapp01`, set up the node, install plugins, set up the required task.  This was the first time setting up a multistage pipeline, which seems like pretty much the same thing in the previous day, only handled a different, and maybe easier to follow way.

And now I think we're done with Jenkins and moving on to Ansible, something I actually have some experience with.
