# Day 61

## Task

There are some applications that need to be deployed on Kubernetes cluster and these apps have some pre-requisites where some configurations need to be changed before deploying the app container. Some of these changes cannot be made inside the images so the DevOps team has come up with a solution to use init containers to perform these tasks during deployment. Below is a sample scenario that the team is going to test first.

    Create a Deployment named as ic-deploy-devops.

    Configure spec as replicas should be 1, labels app should be ic-devops, template's metadata lables app should be the same ic-devops.

    The initContainers should be named as ic-msg-devops, use image fedora with latest tag and use command '/bin/bash', '-c' and 'echo Init Done - Welcome to xFusionCorp Industries > /ic/ecommerce'. The volume mount should be named as ic-volume-devops and mount path should be /ic.

    Main container should be named as ic-main-devops, use image fedora with latest tag and use command '/bin/bash', '-c' and 'while true; do cat /ic/ecommerce; sleep 5; done'. The volume mount should be named as ic-volume-devops and mount path should be /ic.

    Volume to be named as ic-volume-devops and it should be an emptyDir type.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

There's a lot to make here, but same as the others, create a deployment template and then edit that:

```bash
kubectl create deploy ic-deploy-devops --replicas=1 --image=fedora:latest --dry-run=client -oyaml > deployment.yaml
vim deployment.yaml
```

Then go in and add the necessary pieces:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: ic-devops
  name: ic-deploy-devops
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ic-devops
  template:
    metadata:
      labels:
        app: ic-devops
    spec:
      containers:
      - image: fedora:latest
        name: ic-main-devops
        command: ['/bin/bash', '-c', 'while true; do cat /ic/ecommerce; sleep 5; done']
        volumeMounts:
        - name: ic-volume-devops
          mountPath: /ic
      initContainers:
      - image: fedora:latest
        name: ic-msg-devops
        command: ["/bin/bash", "-c", 'echo Init Done - Welcome to xFusionCorp Industries > /ic/ecommerce']
        volumeMounts:
        - name: ic-volume-devops
          mountPath: /ic
      volumes:
      - name: ic-volume-devops
        emptyDir: {}
```

Then just apply the deployment:

```bash
kubectl apply -f deployment.yaml
```

## Validation

First check the status of the deployment:

```bash
kubectl get deploy ic-deploy-devops
```

Hopefully it shows that it's ready.  Then we can check the logs of the main container to see if the init container did its job:

```bash
kubectl logs deploy/ic-deploy-devops -c ic-main-devops
```

It should just show a bunch of lines saying "Init Done - Welcome to xFusionCorp Industries"

## Insights

This task seemed to have a lot of requirements, but they were all things we've done in previous tasks.  It was also able to all be done inside a single manifest which made it simpler than some of the others we've done.

The most important part here was just making sure all the required pieces were in the manifest and that they were correct.  Once everything was in place, all we had to do was apply the manifest, check the logs, and we were done.
