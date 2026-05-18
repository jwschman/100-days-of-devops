# Day 37

## Task

The Nautilus DevOps team possesses confidential data on App Server 1 in the Stratos Datacenter. A container named ubuntu_latest is running on the same server.

Copy an encrypted file /tmp/nautilus.txt.gpg from the docker host to the ubuntu_latest container located at /tmp/. Ensure the file is not modified during this operation.

## Solution

ssh into stapp01 and:

```bash
docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/tmp/nautilus.txt.gpg
```

## Validation

```bash
docker exec -it ubuntu_latest ls -l /tmp/nautilus.txt.gpg
```

## Insights

We could have also used the container ID when copying the file, but we had the name of it so that was easier to send to directly.

I'm more familiar with using volumes to share files between the host and the container, but this is a simple alternative when you just need to copy a single file one way or the other.