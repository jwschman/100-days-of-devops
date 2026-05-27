# Day 56

## Task

Some of the Nautilus team developers are developing a static website and they want to deploy it on Kubernetes cluster. They want it to be highly available and scalable. Therefore, based on the requirements, the DevOps team has decided to create a deployment for it with multiple replicas. Below you can find more details about it:

    Create a deployment using nginx image with latest tag only and remember to mention the tag i.e nginx:latest. Name it as nginx-deployment. The container should be named as nginx-container, also make sure replica counts are 3.

    Create a NodePort type service named nginx-service. The nodePort should be 30011.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

This can be done mostly as `kubectl` commands.

```bash
kubectl create deployment nginx-deployment --image=nginx:latest --replicas=3 --port=80 --dry-run=client -oyaml > deploy.yaml
```

Then go in and edit the container name to nginx-container and apply the deployment:

```bash
vim deploy.yaml
```

It should look like this when you're finished with it:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deployment
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-deployment
  strategy: {}
  template:
    metadata:
      labels:
        app: nginx-deployment
    spec:
      containers:
      - image: nginx:latest
        name: nginx-container
        ports:
        - containerPort: 80
        resources: {}
status: {}
```

Apply the deployment:

```bash
kubectl apply -f deploy.yaml
```

Then we can expose the deployment as a NodePort service:

```bash
kubectl expose deployment nginx-deployment --type=NodePort --name=nginx-service --dry-run=client -oyaml > service.yaml
```

We also need to edit the service to set the nodePort to 30011:

```bash
vim service.yaml
```

and it will look like this when we're done:

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx-deployment
  name: nginx-service
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30011
  selector:
    app: nginx-deployment
  type: NodePort
status:
  loadBalancer: {}
```

Apply the service:

```bash
kubectl apply -f service.yaml
```

## Validation

We can just click the App button at the top to see the nginx welcome page.

## Insights

Pretty easy and mostly done with a couple `kubectl` commands, but we did need to go in and edit a parameter in both the deployment and service.

I'm not sure if there are actually arguments to set the image name, or nodePort inside the `kubectl` commands but I didn't find anything obvious in the docs for it, so I just did `--dry-run=client -oyaml` and edited the files to what the task wanted, and then applied those files.
