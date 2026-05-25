# Day 51

## Task

An application currently running on the Kubernetes cluster employs the nginx web server. The Nautilus application development team has introduced some recent changes that need deployment. They've crafted an image nginx:1.19 with the latest updates.

Execute a rolling update for this application, integrating the nginx:1.19 image. The deployment is named nginx-deployment.

Ensure all pods are operational post-update.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

So we need to edit the deployment:

```bash
kubectl edit deploy nginx-deployment
```

It looks like the update strategy is already rollingUpdate so we just edit the image name and we should be fine:

```yaml
spec:
  template:
    spec:
      containers:
      - image: nginx:1.19
```

## Validation

```bash
kubectl get pods -w
```

Just wait for all the new pods to be up and we should be good.

## Insights

This was just changing the image tag really, so it was quite simple.  It's also something I never do in my cluster since I run everything through GitOps and ArgoCD.  I couldn't do `kubectl edit` in my cluster if I wanted.

Well... I could, but ArgoCD would pick up the drift right away and change it back.