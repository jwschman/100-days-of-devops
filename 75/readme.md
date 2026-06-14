# Day 75

## Task

The Nautilus DevOps team has installed and configured new Jenkins server in Stratos DC which they will use for CI/CD and for some automation tasks. There is a requirement to add all app servers as slave nodes in Jenkins so that they can perform tasks on these servers using Jenkins. Find below more details and accomplish the task accordingly.

Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

1. Add all app servers as SSH build agent/slave nodes in Jenkins. Slave node name for app server 1, app server 2 and app server 3 must be App_server_1, App_server_2, App_server_3 respectively.

2. Add labels as below:

App_server_1 : stapp01

App_server_2 : stapp02

App_server_3 : stapp03

3. Remote root directory for App_server_1 must be /home/tony/jenkins, for App_server_2 must be /home/steve/jenkins and for App_server_3 must be /home/banner/jenkins.

4. Make sure slave nodes are online and working properly.

Note:

1. You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.

2. For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

## Solution

First, set up SSH keys from the Jenkins server to all 3 app servers:

```bash
ssh-keygen
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02
ssh-copy-id banner@stapp03
cat ~/.ssh/id_ed25519
```

Then in Jenkins:

- Go to Manage Jenkins > Plugins, install "SSH Build Agents" plugin and restart
- Go to Manage Jenkins > Credentials > System > Global credentials > Add Credentials
  - Create 3 credentials, one per user (tony, steve, banner), each using "SSH Username with private key" and the same private key content from above
    - To add the private key you need to click "Enter directly" and then "add"

Then add each node via Manage Jenkins > Nodes > New Node:

Set the fields per the task:

- node name: `App_server_1`, `App_server_2`, `App_server_3`
- label should match `stapp01`, `stapp02`, and `stapp03`
- Remote root directories should also match the task

Set "Launch method" to "Launch agents via SSH" for each, set the host name and credentials.  Then save and the nodes should come online automatically.

## Validation

So the nodes didn't come online automatically.  Checking the logs I got this error:

```bash
[06/14/26 12:20:04] [SSH] Starting agent process: cd "/home/tony/jenkins" && java  -jar remoting.jar -workDir /home/tony/jenkins -jar-cache /home/tony/jenkins/remoting/jarCache
Error: LinkageError occurred while loading main class hudson.remoting.Launcher
	java.lang.UnsupportedClassVersionError: hudson/remoting/Launcher has been compiled by a more recent version of the Java Runtime (class file version 61.0), this version of the Java Runtime only recognizes class file versions up to 55.0
Agent JVM has terminated. Exit code=1
[06/14/26 12:20:04] Launch failed - cleaning up connection
[06/14/26 12:20:04] [SSH] Connection closed.
```

So it looked like the Java version on the app servers isn't up to date, so let's just install Java 17 on all of them:

ssh into them and run:

```bash
sudo yum install -y java-17-openjdk
```

In Manage Jenkins > Nodes, all three nodes should show that they're online.  Click each node and check the log to confirm "Agent successfully connected and online"

## Insights

It was my first time adding Jenkins nodes, but it was really just as simple as filling out some fields in the New node menus.  The only real hiccup was that the Java version on the nodes wasn't new enough, but that was solved by quickly updating Java on the nodes.

I likely could have done something fancy and installed Java on all the nodes directly through Jenkins, but my old habits are just sshing in, and that quickly worked for this task.
