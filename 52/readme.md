# Day 52

## Task

Earlier today, the Nautilus DevOps team deployed a new release for an application. However, a customer has reported a bug related to this recent release. Consequently, the team aims to revert to the previous version.

There exists a deployment named nginx-deployment; initiate a rollback to the previous revision.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

So we really just need to check the deployment history and then rollback to the previous revision:

```bash
kubectl rollout history deployment/nginx-deployment
kubectl rollout undo deployment/nginx-deployment
```

## Validation

Just check the status of the pods in the deployment:

```bash
kubectl get pods -w
```

We can also check the deployment history again to confirm that the rollback was successful:

```bash
kubectl rollout history deployment/nginx-deployment
```

## Insights

Again, this isn't something I do in my cluster since I run everything through ArgoCD, but it's good to know how to do it manually in case of an emergency, such as ArgoCD itself being the thing that's broken.