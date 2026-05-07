# Day 21

## Task

The Nautilus development team has provided requirements to the DevOps team for a new application development project, specifically requesting the establishment of a Git repository. Follow the instructions below to create the Git repository on the Storage server in the Stratos DC:

    Utilize yum to install the git package on the Storage Server.

    Create a bare repository named /opt/demo.git (ensure exact name usage).

## Solution

ssh into the storage server and run:

```bash
sudo yum install git -y
sudo mkdir /opt/demo.git
cd /opt/demo.git
sudo git init --bare
```

## Validation

```bash
ls -la /opt/demo.git
```

## Insights

Very simple one, since I've done `git init` dozens of times.