# Day 10

## Task

The production support team of xFusionCorp Industries is working on developing some bash scripts to automate different day to day tasks. One is to create a bash script for taking websites backup. They have a static website running on App Server 1 in Stratos Datacenter, and they need to create a bash script named beta_backup.sh which should accomplish the following tasks. (Also remember to place the script under /scripts directory on App Server 1).

a. Create a zip archive named xfusioncorp_beta.zip of /var/www/html/beta directory.

b. Save the archive in /backup/ on App Server 1. This is a temporary storage, as backups from this location will be cleaned on a weekly basis. Therefore, we also need to save this backup archive on Nautilus Storage Server.

c. Copy the created archive to Nautilus Storage Server server in /backup/ location.

d. Please make sure script won't ask for password while copying the archive file. Additionally, the respective server user (for example, tony in case of App Server 1) must be able to run it.

e. Do not use sudo inside the script.

Note:
The zip package must be installed on given App Server before executing the script. This package is essential for creating the zip archive of the website files. Install it manually outside the script.

## Solution

create the script and make it executable:

```bash
touch /scripts/beta_backup.sh
chmod +x /scripts/beta_backup.sh
```

add the content to the script:

```bash
#!/bin/bash
# Create a zip archive of the beta directory
zip -r /backup/xfusioncorp_beta.zip /var/www/html/beta
# Copy the archive to Nautilus Storage Server
scp /backup/xfusioncorp_beta.zip natasha@ststor01:/backup/
```

Set up passwordless SSH for the user to copy the file without asking for a password.  Default options and no passphrase can be used for simplicity.

```bash
ssh-keygen
ssh-copy-id natasha@ststor01
```

## Validation

check that ssh works without password:

```bash
ssh natasha@ststor01
```

check that the script works:

```bash
/scripts/beta_backup.sh
```

## Insights

The task has a not that the zip package must be installed on the App Server, but I found that it was actually already installed, so I didn't have to do anything about it.

The script is just two commands, one to zip the directory and another to copy it to the storage server.  The passwordless SSH is necessary for it to run without asking for a password, so I had to set that up as well.

The only thing that I really had to check, which is something that always screws me up, is the order of arguments for zip and scp.
