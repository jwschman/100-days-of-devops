# Day 69

## Task

The Nautilus DevOps team has recently setup a Jenkins server, which they want to use for some CI/CD jobs. Before that they want to install some plugins which will be used in most of the jobs. Please find below more details about the task

1. Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

2. Once logged in, install the Git and GitLab plugins. You may need to restart Jenkins to complete the plugin installation; if required, opt to Restart Jenkins when installation is complete and no jobs are running on the plugin installation/update page (Update Centre).

Note:

1. After restarting Jenkins, wait for the login page to reappear before proceeding.

2. For tasks involving web UI changes, capture screenshots to share for review or consider using screen recording software like loom.com for documentation and sharing.

## Solution

So basically just follow the steps in the task:

- Log in to the jenkins server by clicking Jenkins at the top.
- click the "manage settings" gear icon at the top
- click plugins
- click available plugins and search for git and gitlab
- check them to be installed, and then just press install
- on the download and install page we can check "Restart Jenkins when installation is complete and no jobs are running"

After the extensions are installed and the server is restarted, log back in

## Validation

Log back in and go to the plugins page.  Click Available plugins and we should see the git and gitlab extensions available.

## Insights

Well that was easy, and done entirely in the UI.  Doing things just by clicking through the UI makes me a little... uneasy because it's not easily reproducable.  I very much prefer config files and infrastructure-as-code, but I can see how this is definitely more user friendly to someone who doesn't have a background in IaC.

Coming as someone with zero Jenkins exposure this was pretty simple.
