# Day 73

## Task

The devops team of xFusionCorp Industries is working on to setup centralised logging management system to maintain and analyse server logs easily. Since it will take some time to implement, they wanted to gather some server logs on a regular basis. At least one of the app servers is having issues with the Apache server. The team needs Apache logs so that they can identify and troubleshoot the issues easily if they arise. So they decided to create a Jenkins job to collect logs from the server. Please create/configure a Jenkins job as per details mentioned below:

Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321

1. Create a Jenkins jobs named copy-logs.

2. Configure it to periodically build every 2 minutes to copy the Apache logs (both access_log and error_log) from App Server 3 (stapp03) from the default logs location to location /usr/src/itadmin on the Storage Server.

3. Build the job at least once so that the logs are copied and can be verified.

Note:

1. You might need to install some plugins and restart Jenkins. We recommend selecting Restart Jenkins when installation is complete and no jobs are running in the update centre. Refresh the page if the UI gets stuck after a restart.

2. Define the cron expression as required (e.g. */10 * * * * to run every 10 minutes).

3. For scenarios that require web UI changes, take screenshots or record your work (e.g. using loom.com) so you can share it for review if the task is marked incomplete.

## Solution

Same as before:

- Log in to Jenkins
- Create a job called `copy-logs` and set it to freestyle project
- Set the checkbox under Triggers for Build periodically
  - set the schedule to `*/2 * * * *`
- set the Build step to Execute shell and add the following command:

```bash
scp banner@stapp03:/etc/httpd/logs/access_log natasha@ststor01:/usr/src/itadmin/access_log
scp banner@stapp03:/etc/httpd/logs/error_log natasha@ststor01:/usr/src/itadmin/error_log
```

- Save the job

Then we need to add credentials.  The Jenkins server doesn't have passwordless SSH access to stapp03 or ststor01, so we need to add credentials for the Jenkins job to use.  But first we need to install the SSH Credentials plugin:

>In a previous task I just created and copied a key on the jenkins server and the server it was communicating with, but I found this Jenkins plugin which seems to be the more correct way to do things, so I'm going to try it out for this task.

- Click the gear icon
- Add plugins
- Search for SSH Credentials and install it and restart Jenkins

Then we can make and add the credentials:

On the jenkins server:

```bash
ssh-keygen
ssh-copy-id banner@stapp03
ssh-copy-id natasha@ststor01
cat ~/.ssh/id_ed25519
```

- Go to Jenkins dashboard and click on Credentials
- Click on System and then Global credentials (unrestricted)
- Click on Add Credentials
- Select SSH Username with private key
- Enter the username (banner) and the private key (the contents of the id_ed25519 file)
- Click Add
- Go back to the copy-logs job and click Build Now
- Check the console output to see if the logs were copied successfully

I got two errors.  First, it didn't like the default `/var/logs` location so I sshd into `stapp03` and found that it actually saves them to `/etc/httpd/logs/`.  Then the `/usr/src/itadmin` directory didn't exist on ststor01 so I had to ssh into it and create it with `mkdir -p /usr/src/itadmin`.

## Validation

After the job ran successfully I sshd into ststor01 and checked the contents of the `/usr/src/itadmin` directory to see if the logs were copied.  They were, so the task is complete.

## Insights

Ok, so SSH access is something you actually have to think about.  I could have just done things how I previously did with logging into the jenkins server, creating a keypair, and added the public keys to `stapp03` and `ststor01` (which is kind of what happened anyway) but I tried to take a more Jenkins-native approach this time.  It really wasn't any different, other than that the key was created on the jump server rather than the Jenkins server.

There is almost certainly an easier way to handle this, posssibly with a different plugin, but this way worked for me.
