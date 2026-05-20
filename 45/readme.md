# Day 45

## Task

The Nautilus DevOps team is working to create new images per requirements shared by the development team. One of the team members is working to create a Dockerfile on App Server 3 in Stratos DC. While working on it she ran into issues in which the docker build is failing and displaying errors. Look into the issue and fix it to build an image as per details mentioned below:

a. The Dockerfile is placed on App Server 3 under /opt/docker directory.

b. Fix the issues with this file and make sure it is able to build the image.

c. Do not change base image, any other valid configuration within Dockerfile, or any of the data been used — for example, index.html.

Note: Please note that once you click on FINISH button all the existing containers will be destroyed and new image will be built from your Dockerfile.

## Solution

ssh into stapp03 and first lets see what kind of errors we get:


```bash
docker build -t custom-apache2:latest /opt/docker
```

a problem with the certs:

```bash
 => ERROR [6/8] RUN cp certs/server.crt /usr/local/apache2/conf/server.crt                                                                            0.5s
------                                                                                                                                                     
 > [6/8] RUN cp certs/server.crt /usr/local/apache2/conf/server.crt:
0.325 cp: cannot stat 'certs/server.crt': No such file or directory
```

So we should edit the dockerfile:

```bash
vi /opt/docker/Dockerfile
```

Then we have a few things to fix.  Specifically the command to copy files off of the host machine.  `RUN cp` would run that image and not copy things directly off the docker host.  So instead we just change all the `RUN CP`s to `COPY`.

```bash
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' /usr/local/apache2/conf/httpd.conf

RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' /usr/local/apache2/conf/httpd.conf

COPY ./certs/server.crt /usr/local/apache2/conf/server.crt

COPY ./certs/server.key /usr/local/apache2/conf/server.key

COPY ./html/index.html /usr/local/apache2/htdocs/
```

I also changed the path for some of the `sed` commands to the full path rather than relative, since the first one wasn't.

## Validation

```bash
docker build -t app /opt/docker
```

It should build without any problems now.

## Insights

The `RUN cp` actually tripped me up at first because it didn't really look wrong in the code.  But after failing another build, I noticed what was actually wrong and changed the commands to `COPY`, and everything was fine.  

I mentioned before that I have experience writing simple Dockerfiles so I knew what I was looking at when I opened it up in the text editor, but that doesn't mean that you'll notice weird issues right away.
