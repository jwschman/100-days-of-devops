# Day 42

## Task

The Nautilus DevOps team needs to set up several docker environments for different applications. One of the team members has been assigned a ticket where he has been asked to create some docker networks to be used later. Complete the task based on the following ticket description:

a. Create a docker network named as beta on App Server 3 in Stratos DC.

b. Configure it to use bridge drivers.

c. Set it to use subnet 192.168.30.0/24 and iprange 192.168.30.0/24.

## Solution

ssh into stapp03 and:

```bash
docker network create --driver bridge --subnet 192.168.30.0/24 --ip-range 192.168.30.0/24 beta
```

## Validation

```bash
docker network ls
```

## Insights

This is actually something I've been messing around with recently since I've been migrating some of my docker containers from a dedicated docker host to my TrueNAS machine.

All of the points from the task are just options that can be added with the `docker network create` command.  `--driver bridge` could have been omitted since bridge is the default type, but I included it just because the task specifies it.
