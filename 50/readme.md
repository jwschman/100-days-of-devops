# Day 50

## Task

The Nautilus DevOps team has noticed performance issues in some Kubernetes-hosted applications due to resource constraints. To address this, they plan to set limits on resource utilization. Here are the details:

Create a pod named httpd-pod with a container named httpd-container. Use the httpd image with the latest tag (specify as httpd:latest). Set the following resource limits:

Requests: Memory: 15Mi, CPU: 100m

Limits: Memory: 20Mi, CPU: 100m

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

So let's make a template again and then add the request and limits:

```bash
kubectl run httpd-pod --image=httpd:latest --dry-run=client -oyaml > pod.yaml
vi pod.yaml
```

Then we just go in and add the resources and change the name of the container:

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: httpd-pod
  name: httpd-pod
spec:
  containers:
  - image: httpd:latest
    name: httpd-container
    resources:
      requests:
        memory: "15Mi"
        cpu: "100m"
      limits:
        memory: "20Mi"
        cpu: "100m"
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

And then apply it:

```bash
kubectl apply -f pod.yaml
```

## Validation

```bash
kubectl describe pod httpd-pod
```

We should see our resource requests and limits in the output.

## Insights

It's been close to a year, but I still remember all this stuff from studying for the CKA.  I really like working with Kubernetes and writing manifests, so this was another fun, though short, task.

Knowing how to make the templates really helped with the CKA since time was such a factor, and it helps out here as well since I don't have to look up everything in the documentation.
