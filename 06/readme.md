# Day 6

## Task

The Nautilus system admins team has prepared scripts to automate several day-to-day tasks. They want them to be deployed on all app servers in Stratos DC on a set schedule. Before that they need to test similar functionality with a sample cron job. Therefore, perform the steps below:


a. Install cronie package on all Nautilus app servers and start crond service.

b. Add a cron */5 * * * * echo hello > /tmp/cron_text for root user.

## Solution

ssh in to the app servers

```bash
sudo yum install cronie -y
sudo systemctl enable --now crond
sudo crontab -e
```

paste in the following line and save the file

```bash
*/5 * * * * echo hello > /tmp/cron_text
```

## Validation

you could wait 5 minutes and check that /tmp/cron_text file is created and contains the word "hello"

```bash
cat /tmp/cron_text
```

or you can check that the crontab was added successfully:

```bash
sudo crontab -l`
```

running as sudo will edit and show the root's crontab, which is where the task requested to be added

## Insights

It's really easy to miss that the cron job needed to be added for the root user, and if I were to have added it to the current user or just `/etc/crontab` it would not have worked as expected.  Make sure to check the user when adding cron jobs, and note that a user's crontab can be edited with `crontab -e` and listed with `crontab -l` when running as that user, or with `sudo crontab -e` and `sudo crontab -l` to edit and list the root's crontab.
