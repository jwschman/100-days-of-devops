# Day 41

## Task

As per recent requirements shared by the Nautilus application development team, they need custom images created for one of their projects. Several of the initial testing requirements are already been shared with DevOps team. Therefore, create a docker file /opt/docker/Dockerfile (please keep D capital of Dockerfile) on App server 3 in Stratos DC and configure to build an image with the following requirements:

a. Use ubuntu:24.04 as the base image.

b. Install apache2 and configure it to work on 3004 port. (do not update any other Apache configuration settings like document root etc).

## Solution

ssh into stapp03 and:

```bash
vi /opt/docker/Dockerfile
```

next we add this to the Dockerfile:

```bash
from ubuntu:24.04
RUN apt update && apt install -y apache2
RUN sed -i 's/80/3004/g' /etc/apache2/ports.conf
EXPOSE 3004
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

## Validation

```bash
docker build -t custom-apache2:latest /opt/docker
docker run -d -p 3004:3004 custom-apache2:latest
curl http://localhost:3004
```

## Insights

I sometimes have to write Dockerfiles for my go projects, and this was just a single container rather than a multi-step build, so it was a little simpler than what I'm usually doing.  Again, I had to use sed to change the port number, but it's not difficult.  And of course, I almost forgot to actually run apache in the foreground, but fortunately I remembered that part before I built and tested the image.

This one took a little bit to actually check, so I assume that the KodeKloud environment was actually building and testing it just like I did for validation.