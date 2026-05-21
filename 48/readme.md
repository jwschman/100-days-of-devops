# Day 48

## Task

The Nautilus DevOps team is diving into Kubernetes for application management. One team member has a task to create a pod according to the details below:

    Create a pod named pod-httpd using the httpd image with the latest tag. Ensure to specify the tag as httpd:latest.

    Set the app label to httpd_app, and name the container as httpd-container.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

Get a template for the pod and then edit it:

```bash
kubectl run pod-httpd --image=httpd:latest --dry-run=client -oyaml > pod.yaml
vi pod.yaml
```

Then edit to set the app label and container name per the task:

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pod-httpd
    app: httpd_app
  name: pod-httpd
spec:
  containers:
  - image: httpd:latest
    name: httpd-container
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

And then actually apply the pod:

```bash
kubectl apply -f pod.yaml
```

## Validation

```bash
kubectl describe pod pod-httpd
```

## Insights

Now we're into what I like: Kubernetes.  I practiced so many `kubectl run` commands for the CKA that this task only took a few seconds.  I would never actually do `kubectl run` in my homelab, but it was a very good way to complete the CKA tasks quickly and it stuck with me.

After I had the pod template in a file I was able to just go in and edit it to the requirements, which was just adding a label and renaming the container.
