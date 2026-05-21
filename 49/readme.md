# Day 49

## Task

The Nautilus DevOps team is delving into Kubernetes for app management. One team member needs to create a deployment following these details:

Create a deployment named nginx to deploy the application nginx using the image nginx:latest (ensure to specify the tag)

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

Just like the last one, only we don't need the template, we can just deploy it as it is:

```bash
kubectl create deployment nginx --image=nginx:latest
```

## Validation

```bash
kubectl describe deploy nginx
```

## Insights

Even simpler than the last one since I didn't have to edit it at all.  It could all just be done with one line.

I'm actually almost certain I had a flash card in my CKA deck with a solution like the one I used in this, although that one may have also included the number of desired replicas as well.
