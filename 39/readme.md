# Day 39

## Task

One of the Nautilus developer was working to test new changes on a container. He wants to keep a backup of his changes to the container. A new request has been raised for the DevOps team to create a new image from this container. Below are more details about it:

a. Create an image official:nautilus on Application Server 1 from a container ubuntu_latest that is running on same server.

## Solution

ssh into stapp01 and:

```bash
docker commit -p ubuntu_latest official:nautilus
```

## Validation

```bash
docker images
```

## Insights

I've never had to actually create an image from an existing container before so I had to look it up, but like most docker commands, it was very straightforward.  The `-p` flag is used to pause the container during the commit to ensure that the filesystem doesn't change while the image is being created.