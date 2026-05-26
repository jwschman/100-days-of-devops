# Day 54

## Task

We are working on an application that will be deployed on multiple containers within a pod on Kubernetes cluster. There is a requirement to share a volume among the containers to save some temporary data. The Nautilus DevOps team is developing a similar template to replicate the scenario. Below you can find more details about it.

    Create a pod named volume-share-datacenter.

    For the first container, use image fedora with latest tag only and remember to mention the tag i.e fedora:latest, container should be named as volume-container-datacenter-1, and run a sleep command for it so that it remains in running state. Volume volume-share should be mounted at path /tmp/blog.

    For the second container, use image fedora with the latest tag only and remember to mention the tag i.e fedora:latest, container should be named as volume-container-datacenter-2, and again run a sleep command for it so that it remains in running state. Volume volume-share should be mounted at path /tmp/cluster.

    Volume name should be volume-share of type emptyDir.

    After creating the pod, exec into the first container i.e volume-container-datacenter-1, and just for testing create a file blog.txt with the content Welcome to xFusionCorp Industries under the mounted path of first container i.e /tmp/blog.

    The file blog.txt should be present under the mounted path /tmp/cluster on the second container volume-container-datacenter-2 as well, since they are using a shared volume.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

So we're just going to need to write a manifest with multiple containers and a shared volume.  Plenty of places to copy in the kubernetes documentation.  The page I'll be looking at is <https://kubernetes.io/docs/concepts/storage/volumes/>:

```bash
vim pod.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: volume-share-datacenter
spec:
  containers:
  - image: fedora:latest
    name: volume-container-datacenter-1
    command: ["sleep", "infinity"]
    volumeMounts:
    - mountPath: /tmp/blog
      name: volume-share
  - image: fedora:latest
    name: volume-container-datacenter-2
    command: ["sleep", "infinity"]
    volumeMounts:
    - mountPath: /tmp/cluster
      name: volume-share
  volumes:
  - name: volume-share
    emptyDir: {}
```

Then apply the manifest and execute the command to create the file in the first container:

```bash
kubectl apply -f pod.yaml
kubectl get pods
kubectl exec -it volume-share-datacenter -c volume-container-datacenter-1 -- bash
echo "Welcome to xFusionCorp Industries" > /tmp/blog/blog.txt
exit
```

## Validation

Just check that the file is present in the second container:

```bash
kubectl exec -it volume-share-datacenter -c volume-container-datacenter-2 -- bash
ls /tmp/cluster
cat /tmp/cluster/blog.txt
exit
```

I could have just ran those commands with `kubectl exec` but since I already shelled in to the first pod, just going into the second one was easier.

## Insights

I haven't looked at the Kubernetes documentation for pod templates for quite a while now so it gave me more CKA flashbacks.

Setting up multiple containers in the pod is simple, and the shared volume is also as simple as just referencing the same volume name in both containers.