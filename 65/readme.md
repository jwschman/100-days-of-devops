# Day 65

## Task

The Nautilus application development team observed some performance issues with one of the application that is deployed in Kubernetes cluster. After looking into number of factors, the team has suggested to use some in-memory caching utility for DB service. After number of discussions, they have decided to use Redis. Initially they would like to deploy Redis on kubernetes cluster for testing and later they will move it to production. Please find below more details about the task:

Create a redis deployment with following parameters:

    Create a config map called my-redis-config having maxmemory 2mb in redis-config.

    Name of the deployment should be redis-deployment, it should use
    redis:alpine image and container name should be redis-container. Also make sure it has only 1 replica.

    The container should request for 1 CPU.

    Mount 2 volumes:

    a. An Empty directory volume called data at path /redis-master-data.

    b. A configmap volume called redis-config at path /redis-master.

    c. The container should expose the port 6379.

    Finally, redis-deployment should be up and running.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

We can get the configmap template from the kubernetes docs: <https://kubernetes.io/docs/concepts/configuration/configmap/>

```bash
vim cm.yaml
```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-redis-config
data:
  redis-config: |
    maxmemory 2mb
```

Apply that, and then create the deployment template:

```bash
kubectl apply -f cm.yaml
kubectl create deploy redis-deployment --image=redis:alpine --replicas=1 --dry-run=client -oyaml > deploy.yaml
vim deploy.yaml
```

Then add the specs from the task:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis-deployment
  name: redis-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-deployment
  template:
    metadata:
      labels:
        app: redis-deployment
    spec:
      containers:
      - image: redis:alpine
        name: redis-container
        resources: {}
        volumeMounts:
        - name: data
          mountPath: /redis-master-data
        - name: redis-config
          mountPath: /redis-master
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: "1"
      volumes:
      - name: data
        emptyDir: {}
      - name: redis-config
        configMap:
          name: my-redis-config
```

Then just apply the deployment and we should be good to go:

```bash
kubectl apply -f deploy.yaml
```

## Validation

```bash
kubectl get pods
```

It should show the redis-deployment pod in running state.

## Insights

Once again we're mostly using pieces from the other tasks, but this time we're also using a configmap.  How to make and use configmaps is pretty easy to check out in the docs.  Just remember that you also need to mount the configmap as a volume in the deployment.

Also, redis is something I'm actually running inside my cluster, but I have it installed via helm which is a completely different way of doing things than we use here.
