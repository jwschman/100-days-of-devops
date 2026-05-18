# Day 40

## Task

One of the Nautilus DevOps team members was working to configure services on a kkloud container that is running on App Server 1 in Stratos Datacenter. Due to some personal work he is on PTO for the rest of the week, but we need to finish his pending work ASAP. Please complete the remaining work as per details given below:

a. Install apache2 in kkloud container using apt that is running on App Server 1 in Stratos Datacenter.

b. Configure Apache to listen on port 5000 instead of default http port. Do not bind it to listen on specific IP or hostname only, i.e it should listen on localhost, 127.0.0.1, container ip, etc.

c. Make sure Apache service is up and running inside the container. Keep the container in running state at the end.

## Solution

SSH into stapp01 and:

```bash
docker exec -it kkloud /bin/bash
apt update
apt install apache2 -y
sed -i 's/Listen 80/Listen 5000/g' /etc/apache2/ports.conf
service apache2 restart
service apache2 status
``` 

## Validation

Still inside the container:

```bash
curl -I http://localhost:5000
```

## Insights

So this was the most in depth task of the day, and it was pretty good for learning how to work with containers and the services inside them.  The actual task was very straightforward, but once you get into the container you're working on a very stripped down version of linux, and you have to know how to work with it.

Installing the package was easy, but I didn't have access to even `vi` in the container, so I had to use `sed` to edit the config.  It's a tool that I technically know how to use, but I don't use it often so I was definitely rusty with it.

Also, there wasn't any `systemctl` to manage the service, so I had to use `service` instead which reminded me of my FreeBSD days.