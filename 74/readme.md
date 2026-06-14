# Day 74

## Task

There is a requirement to create a Jenkins job to automate the database backup. Below you can find more details to accomplish this task:

Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

    Create a Jenkins job named database-backup.

    Configure it to take a database dump of the kodekloud_db01 database present on the App server (stapp01) in Stratos Datacenter, the database user is kodekloud_roy and password is asdfgdsd.

    The dump should be named in db_$(date +%F).sql format, where date +%F is the current date.

    Copy the db_$(date +%F).sql dump to the Storage server (ststor01) under location /home/natasha/db_backups.

    Further, schedule this job to run periodically at */10 * * * * (please use this exact schedule format).

Note:

    You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case please make sure to refresh the UI page.

    Please make sure to define you cron expression like this */10 * * * * (this is just an example to run job every 10 minutes).

    For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

## Solution

OK we're going to need a SSH keypair again.  I'm going to try to keep things simple and do this from the jenkins server.  Once there:

```bash
ssh-keygen
ssh-copy-id tony@stapp01
ssh-copy-id natasha@ststor01
cat ~/.ssh/id_ed25519
```

Then we're going to install the SSH plugin in jenkins again:

- Click the gear icon
- Add plugins
- Search for SSH Credentials and install it and restart Jenkins
- Go to Jenkins dashboard and click on Credentials
- Click on System and then Global credentials (unrestricted)
- Click on Add Credentials
- Select SSH Username with private key
- Enter private key (the contents of the id_ed25519 file)
- Click Add
- Go back to the top jenkins menu and add a new job
- Name it database-backup and set type to Freestyle Project
- Click add build step of type Execute shell

Then in the Command we'll use this:

```bash
DUMP_FILE="db_$(date +%F).sql"
ssh tony@stapp01 "mysqldump -u kodekloud_roy -p asdfgdsd kodekloud_db01" > /tmp/${DUMP_FILE}
scp /tmp/${DUMP_FILE} natasha@ststor01:/home/natasha/db_backups/${DUMP_FILE}
```

- Under Triggers, check Build periodically and enter `*/10 * * * *`
- Save the job and click "Build Now" to verify it works

## Validation

After running the job manually check that the dump landed on the storage server
    
```bash
ssh natasha@ststor01 "ls -la /home/natasha/db_backups/"
```

You should see a file like `db_2026-06-14.sql`.

## Insights

So I'm kind of thinking adding the ssh key to Jenkins is actually unnecessary since we're already doing it directly from the Jenkins server and doing `ssh-copy-id` to the other servers, but it might be a good practice to have it there anyway.

They way I set up the script was to dump the sql database to the jenkins server and then scp'd that to the storage server.  We could have also had it dump directly on the app server, and then scp'd it to the storage server, but that would have also required setting up passwordless access between the two servers which seemed like an unnecessary step for this task.

Being able to just use a cron job style schedule as a build trigger is also pretty handy.

Also, these Jenkins tasks sure take a while to Check...
