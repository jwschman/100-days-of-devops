# Day 66

## Task

A new MySQL server needs to be deployed on Kubernetes cluster. The Nautilus DevOps team was working on to gather the requirements. Recently they were able to finalize the requirements and shared them with the team members to start working on it. Below you can find the details:

1.) Create a PersistentVolume mysql-pv, its capacity should be 250Mi, set other parameters as per your preference.

2.) Create a PersistentVolumeClaim to request this PersistentVolume storage. Name it as mysql-pv-claim and request a 250Mi of storage. Set other parameters as per your preference.

3.) Create a deployment named mysql-deployment, use any mysql image as per your preference. Mount the PersistentVolume at mount path /var/lib/mysql.

4.) Create a NodePort type service named mysql and set nodePort to 30007.

5.) Create a secret named mysql-root-pass having a key pair value, where key is password and its value is YUIidhb667, create another secret named mysql-user-pass having some key pair values, where first key is username and its value is kodekloud_aim, second key is password and value is BruCStnMT5, create one more secret named mysql-db-url, key name is database and value is kodekloud_db9

6.) Define some environment variables within the container:

a.) name: MYSQL_ROOT_PASSWORD, should pick value from secretKeyRef name: mysql-root-pass and key: password

b.) name: MYSQL_DATABASE, should pick value from secretKeyRef name: mysql-db-url and key: database

c.) name: MYSQL_USER, should pick value from secretKeyRef name: mysql-user-pass key key: username

d.) name: MYSQL_PASSWORD, should pick value from secretKeyRef name: mysql-user-pass and key: password

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

Let's start with the volumes:

```bash
vim storage.yaml
```

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 250Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 250Mi
```

Then let's apply it:

```bash
kubectl apply -f storage.yaml
```

Looking at the list the next thing we should probably create is the secrets.  Normally I like to create manifests, but for this task we can just use `kubectl create` directly

```bash
kubectl create secret generic mysql-root-pass \
  --from-literal=password=YUIidhb667

kubectl create secret generic mysql-user-pass \
  --from-literal=username=kodekloud_aim \
  --from-literal=password=BruCStnMT5

kubectl create secret generic mysql-db-url \
  --from-literal=database=kodekloud_db9
```

Now let's create the deployment:

```bash
kubectl create deploy mysql-deployment --image=mysql:latest --replicas=1 --dry-run=client -oyaml > deploy.yaml
vim deploy.yaml
```

We should also add the environment variables from step 6.

>It should be noted that mysql uses port 3306, so we also have to add that

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: mysql-deployment
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql-deployment
  strategy: {}
  template:
    metadata:
      labels:
        app: mysql-deployment
    spec:
      containers:
      - image: mysql:latest
        name: mysql
        ports:
        - containerPort: 3306 # don't forget this
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
            valueFrom:
            secretKeyRef:
                name: mysql-root-pass
                key: password
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
                name: mysql-db-url
                key: database
        - name: MYSQL_USER
          valueFrom:
          secretKeyRef:
                name: mysql-user-pass
                key: username
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
                name: mysql-user-pass
                key: password
      volumes:
        - name: mysql-storage
          persistentVolumeClaim:
            claimName: mysql-pv-claim
```

Next apply that and get to work on the service:

```bash
kubectl apply -f deploy.yaml
kubectl expose deploy mysql-deployment --type=NodePort --port=3306 --dry-run=client -oyaml > network.yaml
vim network.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: mysql-deployment
  name: mysql # don't forget to set this
spec:
  ports:
  - port: 3306
    protocol: TCP
    targetPort: 3306
    nodePort: 30007 # add this line
  selector:
    app: mysql-deployment
  type: NodePort
status:
  loadBalancer: {}
```

Then apply the service and we should be good to go:

```bash
kubectl apply -f network.yaml
```

## Validation

```bash
kubectl get pv,pvc,deploy,svc,secret
kubectl get pods
```

Everything in the output should show what we created above, and the pod should be ready and the PVC should be bound.

## Insights

Ok that was a lot, and it didn't help that the steps in the task were poorly laid out (probably by design).  

Because of the way things were written it would have been very easy to miss things, and had we applied the deployment without setting the env it would never have gone into a ready state because mysql requires some of those variables to be set.

Nothing in this task was particularly hard, there were just a lot of things that needed to be wired together correctly.  Good thing I went back and checked several times.
