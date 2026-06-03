# Day 68

## Task

The DevOps team at xFusionCorp Industries is initiating the setup of CI/CD pipelines and has decided to utilize Jenkins as their server. Execute the task according to the provided requirements:

1. Install Jenkins on the jenkins server using the apt utility only, and start it using the service command.

    If you face a timeout issue while starting the Jenkins service, first check the service status with service jenkins status
    Then review the logs in /var/log/jenkins/jenkins.log to identify the cause.

2. Jenkin's admin user name should be theadmin, password should be Adm!n321, full name should be Ammar and email should be ammar@jenkins.stratos.xfusioncorp.com.

Note:

1. To access the jenkins server, connect from the jump host using the root user with the password S3curePass.

2. After Jenkins server installation, click the Jenkins button on the top bar to access the Jenkins UI and follow on-screen instructions to create an admin user.

## Solution

So first we need to ssh into the Jenkins server, which we can see in the notes at the bottom of the task.

Then we're going to follow the official guide on the Jenkins website to install it.  First we need to install OpenJDK

```bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre -y
java -version
```

We should see that OpenJDK is installed.  Next we add the repo and install Jenkins.  I'll go with the LTS release

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins -y
```

Then just start it using the service command as recommended in the task:

```bash
service jenkins start
service jenkins status
```

We should see that jenkins is running.  Click the Jenkins tab at the top to go to the Jenkins page.

It'll show that it wants a pregenerated admin password, so we cat that out to get the password back on the jenkins host:

```bash
cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy the output into the Jenkins page and go through the Getting Started Wizard.  I just selected the recommended plugins install.

Then when I got to the admin user creation I just entered the information from the task.

- Username: theadmin
- Password: Adm!n321
- Full Name: Ammar
- E-Mail address: ammar@jenkins.stratos.xfusioncorp.com

Then just save and finish.  

## Validation

Check if user was created successfully by logging out and back in with the credentials we set up.

## Insights

So I'm going to be honest, I've never actually used Jenkins so this will be a learning experience for me.  Installing and starting it was basic Linux admin tasks, and then setting up the admin user was done through the UI.

Jenkins is definitely a lot more UI driven than other DevOps tools I've used, which is a bit of a change of pace.  I'm used to doing everything through the command line and configuration files, so this will be interesting to get used to.
