# Day 38

## Task

Nautilus project developers are planning to start testing on a new project. As per their meeting with the DevOps team, they want to test containerized environment application features. As per details shared with DevOps team, we need to accomplish the following task:

a. Pull busybox:musl image on App Server 2 in Stratos DC and re-tag (create new tag) this image as busybox:media.

## Solution

ssh into stapp02 and:

```bash
docker pull busybox:musl
docker tag busybox:musl busybox:media
```

## Validation

```bash
docker images | grep busybox
```

## Insights

These were pretty basic docker commands.  Pulling an image from the registry and re-tagging it is a simple process.  Again, my LFCS test and homelab work have given me a lot of practice with these commands so I was able to complete this without any issues.