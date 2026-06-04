# Day 70

## Task

The Nautilus team is integrating Jenkins into their CI/CD pipelines. After setting up a new Jenkins server, they're now configuring user access for the development team, Follow these steps:

1. Click on the Jenkins button on the top bar to access the Jenkins UI. Login with username admin and password Adm!n321.

2. Create a jenkins user named ravi with the password TmPcZjtRQx. Their full name should match Ravi.

3. Utilize the Project-based Matrix Authorization Strategy to assign overall read permission to the ravi user.

4. Remove all permissions for Anonymous users (if any) ensuring that the admin user retains overall Administer permissions.

5. For the existing job, grant ravi user only read permissions, disregarding other permissions such as Agent, SCM etc.

Note:

1. You may need to install plugins and restart Jenkins service. After plugins installation, select Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page.

2. After restarting the Jenkins service, wait for the Jenkins login page to reappear before proceeding. Avoid clicking Finish immediately after restarting the service.

3. Capture screenshots of your configuration for review purposes. Consider using screen recording software like loom.com for documentation and sharing.

## Solution

Same as the last one, we're just going to click through the UI to set this up.  First login and then:

- Click the gear icon at the top
- Go to Users
- Click Create User
- Create the ravi user with the info from the task

So it looks like the Matrix Authorization Strategy may be a plugin, so we need to install it first:

- Back at the gear icon, click Plugins
- Go to available and search for Matrix Authorization Strategy
- Install it and restart Jenkins
- After restarting and logging back in, go to the gear icon and click Security
- Under Authorization, select Project-based Matrix Authorization Strategy
- Make sure the Anonymous user has no permissions
- Click add user and input ravi, then give them overall read permissions as well as read permissions for the existing Job
- Also add the admin user and make sure they have overall Administer permissions
- Click Save

## Validation

Log out and log back in as ravi, then check that you can only see the existing job and have read permissions for it.  You should not have any other permissions.

Log back in as admin and make sure you have permissions to do everything.

## Insights

I'm learning as I'm going here so there was a little bump when I couldn't find the Matrix Authorization Strategy in the authorization settings.  After a quick search I found out that it's set up using a plugin, so after I installed that things were pretty self explanatory.  All I had to do was follow the remaining instructions.
