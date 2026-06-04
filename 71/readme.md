# Day 71

## Task

Some new requirements have come up to install and configure some packages on the Nautilus infrastructure under Stratos Datacenter. The Nautilus DevOps team installed and configured a new Jenkins server so they wanted to create a Jenkins job to automate this task. Find below more details and complete the task accordingly:

1. Access the Jenkins UI by clicking on the Jenkins button in the top bar. Log in using the credentials: username admin and password Adm!n321.

2. Create a new Jenkins job named install-packages and configure it with the following specifications:

    Add a string parameter named PACKAGE.

    Configure the job to install a package specified in the $PACKAGE parameter on the storage server (Stratos Datacenter).

    Build the job at least once (e.g. with parameter PACKAGE=vim-enhanced) so the package is installed on the Storage server and can be verified.

Note:

1. Ensure to install any required plugins and restart the Jenkins service if necessary. Opt for Restart Jenkins when installation is complete and no jobs are running on the plugin installation/update page. Refresh the UI page if needed after restarting the service.

2. Verify that the Jenkins job runs successfully on repeated executions to ensure reliability.

3. Capture screenshots of your configuration for documentation and review purposes. Alternatively, use screen recording software like loom.com for comprehensive documentation and sharing.


## Solution

First it looks like we're going to need passwordless SSH access to the storage server.  So we're going to generate a keypair on the Jenkins server and copy the public key to the storage server.  First SSH into the Jenkins server and:

```bash
ssh-keygen -t ed25519 -N ""
ssh-copy-id -i /var/lib/jenkins/.ssh/id_ed25519.pub natasha@ststor01
```

Next we'll go in and create the task in Jenkins.

Click new project and then just follow the instructions:

- Name it install-packages and click "freestyle package" and click next
- Click on "This project is parameterized" and add a string parameter named PACKAGE and click save
- Click on "Add build step" and select "Execute shell" and add the following command:

```
ssh natasha@ststor01 "sudo dnf install -y $PACKAGE"
```

- Click save
- Then click Build with Parameters
- enter vim-enhanced for the PACKAGE parameter and click Build

## Validation

The build should succeed.  We can click Console Output and see that vim-enhanced installed successfully.

## Insights

It took a little bit longer than I expected to set this up, but again, it's because I have no experience with Jenkins.  After a bit of searching around I found that I can just enter shell commands for build steps, so I figured that would be the way to go.  I also had to find out how to add the parameter.

I figured running an SSH command to install it was the easiest way, so I had to set up passwordless SSH between the jenkins server and the storage server.  Once that was done, all I had to do was write the command, then click build, and everything worked out.
