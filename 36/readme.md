# Day 36

## Task

The Nautilus DevOps team is conducting application deployment tests on selected application servers. They require a nginx container deployment on Application Server 3. Complete the task with the following instructions:

    On Application Server 3 create a container named nginx_3 using the nginx image with the alpine tag. Ensure container is in a running state.

## Solution

ssh into stapp03 and:

```bash
docker run -d --name nginx_3 nginx:alpine
```

## Validation

```bash
docker ps
```

## Insights

I may have initially called the container `nginx` rather than `nginx_3` like the instructions say, so I killed my streak here.  Remember to check the instructions carefully.

Other than that, the actual task of pulling and running the image was extremely simple and done with one line.  LFCS really got me used to this.