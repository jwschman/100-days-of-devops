# Day 43

## Task

The Nautilus DevOps team is planning to host an application on a nginx-based container. There are number of tickets already been created for similar tasks. One of the tickets has been assigned to set up a nginx container on Application Server 3 in Stratos Datacenter. Please perform the task as per details mentioned below:

a. Pull nginx:stable docker image on Application Server 3.

b. Create a container named apps using the image you pulled.

c. Map host port 6100 to container port 80. Please keep the container in running state.

## Solution

ssh into stapp03 and:

```bash
docker pull nginx:stable
docker run -d --name apps -p 6100:80 nginx:stable
```

## Validation

```bash
docker ps
```

It should show the nginx container running and mapping port 6100->80.

## Insights

Studying for the LFCS exam really taught me how to do these docker commands.  The task made it seem like it would have been an extra step, but it was just a one liner to do the whole thing.  I didn't really even need to pull the image first, since the run command will pull it if it doesn't exist, but I was just following the instructions as they were given.
