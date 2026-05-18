# Day 35

## Task

The Nautilus DevOps team aims to containerize various applications following a recent meeting with the application development team. They intend to conduct testing with the following steps:

    Install docker-ce and docker compose packages on App Server 2.

    Initiate the docker service.

## Solution

ssh into stapp02 and get docker installed.  First we have to install the repository:

```bash
sudo dnf -y install dnf-plugins-core
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf -y install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl enable --now docker
```

## Validation

```bash
sudo docker run hello-world
```

## Insights

A quick one.  I've installed docker on dozens of machines so this was nothing too hard.

Also, it looks like we're out of the git chapter and into the docker one now.