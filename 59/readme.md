# Day 59

## Task

Last week, the Nautilus DevOps team deployed a redis app on Kubernetes cluster, which was working fine so far. This morning one of the team members was making some changes in this existing setup, but he made some mistakes and the app went down. We need to fix this as soon as possible. Please take a look.

The deployment name is redis-deployment. The pods are not in running state right now, so please look into the issue and fix the same.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

First let's check the status of the deployment and its pods

```bash
kubectl describe deploy redis-deployment
kubectl get pods
kubectl describe pod redis-deployment-6bc546f779-dk5tc
```

From that we see that the pod is stuck in ContainerCreating, and the events for the pod shows:

```text
Warning  FailedMount  26s (x7 over 57s)  kubelet            MountVolume.SetUp failed for volume "config" : configmap "redis-conig" not found
```

It really looks like a typo for the name of the configmap.  Confirm with:

```bash
kubectl get cm
```

And we can see the actual configmap name is `redis-config` so we can just edit the live deployment to fix it:

```bash
kubectl edit deploy redis-deployment
```

And fix the name of the configmap being mounted:

```yaml
      volumes:                                      
      - emptyDir: {}                  
        name: data           
      - configMap:                     
          defaultMode: 420   
          name: redis-config   # this is the part we change
```

Check if things are ok with:

```yaml
kubectl get pods
```

And we can see that we have a new problem, `ErrImagePull`.  So we can just go back in to the config and fix the image name:

```bash
kubectl edit deploy redis-deployment
```

```yaml
    spec:
      containers:
      - image: redis:alpine # alpine not alpin
```

Check the status of the pods again:

```bash
kubectl get pods
```

## Validation

If the previous `kubectl get pods` showed the pod status was running, we're good.

## Insights

Typos are very real and can be infuriating, but these two were pretty easy to find because they were obvious misspellings of things, and also things that would show up in the events for a pod.

Being able to just do `kubectl edit` for the deployments in this situation is quite easy, but in my actual environment I'd have to make the changes to the manifests and push to git before argocd actually picked up the changes.
