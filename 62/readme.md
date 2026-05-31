# Day 62

## Task

The Nautilus DevOps team is working to deploy some tools in Kubernetes cluster. Some of the tools are licence based so that licence information needs to be stored securely within Kubernetes cluster. Therefore, the team wants to utilize Kubernetes secrets to store those secrets. Below you can find more details about the requirements:

    We already have a secret key file official.txt under the /opt/ directory. Create a generic secret named official, it should contain the password/license-number present in official.txt file.

    Also create a pod named secret-xfusion.

    Configure pod's spec as container name should be secret-container-xfusion, image should be debian with latest tag (remember to mention the tag with image). Use sleep command for container so that it remains in running state. Consume the created secret and mount it under /opt/games within the container.

    To verify you can exec into the container secret-container-xfusion, to check the secret key under the mounted path /opt/games. Before hitting the Check button please make sure pod/pods are in running state, also validation can take some time to complete so keep patience.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

Well first we have to make the secret, and then we'll make the pod.  I'll be using these pages for secrets: <https://kubernetes.io/docs/concepts/configuration/secret/> and <https://kubernetes.io/docs/reference/kubectl/generated/kubectl_create/kubectl_create_secret_generic/>

```bash
kubectl create secret generic official --from-file=/opt/official.txt
kubectl run secret-xfusion --image=debian:latest --dry-run=client -oyaml > pod.yaml
vim pod.yaml
```

Next edit the pod to include the specs and the secret:

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: secret-xfusion
  name: secret-xfusion
spec:
  containers:
  - image: debian:latest
    name: secret-container-xfusion
    command: ["sleep", "infinity"]
    volumeMounts:
    - name: secret-volume
      mountPath: /opt/games
  volumes:
   - name: secret-volume
     secret:
      secretName: official
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

Then just apply the pod:

```bash
kubectl apply -f pod.yaml
```

## Validation

Just follow the instructions and check the contents of the secret:

```bash
kubectl exec -it secret-xfusion -- cat /opt/games/official.txt
```

## Insights

I haven't messed with secrets like this in a long time, so I definitely needed to check the documentation here again.  Following the documentation was easy enough and I was able to create the secret direclty using `kubectl create secret` and using `--from-file` to make things even easier.

In my own cluster I use Vault running externally to store my secrets, and then external secrets operator to pull them in.  I have a bash script for making the external secrets manifests so doing things this way once again reminded me of doing things for the CKA rather than how I actually do them myself.
