# Day 60

## Task

The Nautilus DevOps team is working on a Kubernetes template to deploy a web application on the cluster. There are some requirements to create/use persistent volumes to store the application code, and the template needs to be designed accordingly. Please find more details below:

    Create a PersistentVolume named as pv-nautilus. Configure the spec as storage class should be manual, set capacity to 5Gi, set access mode to ReadWriteOnce, volume type should be hostPath and set path to /mnt/dba (this directory is already created, you might not be able to access it directly, so you need not to worry about it).

    Create a PersistentVolumeClaim named as pvc-nautilus. Configure the spec as storage class should be manual, request 1Gi of the storage, set access mode to ReadWriteOnce.

    Create a pod named as pod-nautilus, mount the persistent volume you created with claim name pvc-nautilus at document root of the web server, the container within the pod should be named as container-nautilus using image httpd with latest tag only (remember to mention the tag i.e httpd:latest).

    Create a node port type service named web-nautilus using node port 30008 to expose the web server running within the pod.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

So I'm going to be referencing the Kubernetes docs to get the templates for these.  Specifically <https://kubernetes.io/docs/concepts/storage/persistent-volumes/>

A trick I learned when studying for the CKA was to just load the page, and then do a search for `kind: persistentvolume` or whatever actual resource you were trying to create to jump directly to a template.

First we need to create the PV and PVC:

```bash
vim storage.yaml
```

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nautilus
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  hostPath:
    path: "/mnt/dba"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nautilus
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: manual
```

Apply it and also create the pod template:

```bash
kubectl apply -f storage.yaml
kubectl run pod-nautilus --image=httpd:latest --dry-run=client -oyaml > pod.yaml
```

Then go in and change the container name and add the storage:

```bash
vim pod.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pod-nautilus
  name: pod-nautilus
spec:
  volumes:
    - name: pvc-nautilus
      persistentVolumeClaim:
        claimName: pvc-nautilus
  containers:
  - image: httpd:latest
    name: container-nautilus
    volumeMounts:
      - mountPath: "/usr/local/apache2/htdocs/"
        name: pvc-nautilus
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

Apply that and create the service:

```bash
kubectl apply -f pod.yaml
kubectl expose pod pod-nautilus --type=NodePort --name=web-nautilus --port=80 --dry-run=client -oyaml > service.yaml
```

Then edit the service to add the nodePort:

```bash
vim servcie.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    run: pod-nautilus
  name: web-nautilus
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30008 # add this
  selector:
    run: pod-nautilus
  type: NodePort
status:
  loadBalancer: {}
```

Then apply the service:

```bash
kubectl apply -f service.yaml
```

## Validation

It's not a bad idea to check if the pod is actually running:

```bash
kubectl get pods
```

If it's ready, click the Website tab at the top of the page, and you should see a page displayed.

## Insights

Nothing in this task was particularly hard, there were just several steps that needed to be done for everything to work correctly together.  We couldn't make the pod before making the PV and PVC, we couldn't make the service before making the pod.

Fortunately the documentation for creating PVs and PVCs is pretty simple, and I was able to find templates to use for them and just had to fill in the details from the task.  Then once I had a pod template I was able to also use the PV documentation to add the volume to the pod.

Basically, the Kubernetes documentation is your friend in a task like this.
