# Day 53

## Task

We encountered an issue with our Nginx and PHP-FPM setup on the Kubernetes cluster this morning, which halted its functionality. Investigate and rectify the issue:

The pod name is nginx-phpfpm and configmap name is nginx-config. Identify and fix the problem.

Once resolved, copy /home/thor/index.php file from the jump host to the nginx-container within the nginx document root. After this, you should be able to access the website using Website button on the top bar.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

First we should check the configmap and the pod to see what's going on:

```bash
kubectl describe configmap nginx-config
kubectl describe pod nginx-phpfpm
```

There's a discrepency in the configmap and pod: the files are mounted at `/usr/share/nginx/html` in the pod rather than `/var/www/html` like the configmap wants, so let's update the pod:

```bash
kubectl get pod nginx-phpfpm -o yaml > nginx-phpfpm.yaml
```

Then we can edit the yaml file to change the mount path:

```bash
vi nginx-phpfpm.yaml
```

```yaml
spec:
  containers:
  - image: nginx:latest
    volumeMounts:
    - mountPath: /var/www/html
      name: shared-files
  volumes:
  - emptyDir: {}
    name: shared-files
```

Then we need to delete and reapply the pod from the manifest we made:

```bash
kubectl delete pod nginx-phpfpm
kubectl apply -f nginx-phpfpm.yaml
```

Once the pod is up we can copy the file over to the document root:

```bash
kubectl cp /home/thor/index.php nginx-phpfpm:/var/www/html/index.php -c nginx-container
```

## Validation

Click the website button at the top and it hsould show the phpinfo() page

## Insights

Volume mounts can always be tricky, and the fact that this was using multiple images, multiple directories, and a configMap with its own specified path for the files made it a bit more difficult to keep track of where the files were supposed to be.

Once I lined up everything to be at `var/www/html` it was a lot easier to get the fix working.

One small snag was that the pod isn't part of a deployment, so I had to delete and reapply it after making the changes.

This was the trickiest Kubernetes task so far.
