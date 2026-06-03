# Day 67

## Task

The Nautilus Application development team has finished development of one of the applications and it is ready for deployment. It is a guestbook application that will be used to manage entries for guests/visitors. As per discussion with the DevOps team, they have finalized the infrastructure that will be deployed on Kubernetes cluster. Below you can find more details about it.

BACK-END TIER

    Create a deployment named redis-master for Redis master.

    a.) Replicas count should be 1.

    b.) Container name should be master-redis-nautilus and it should use image redis.

    c.) Request resources as CPU should be 100m and Memory should be 100Mi.

    d.) Container port should be redis default port i.e 6379.

    Create a service named redis-master for Redis master. Port and targetPort should be Redis default port i.e 6379.

    Create another deployment named redis-slave for Redis slave.

    a.) Replicas count should be 2.

    b.) Container name should be slave-redis-nautilus and it should use gcr.io/google_samples/gb-redisslave:v3 image.

    c.) Requests resources as CPU should be 100m and Memory should be 100Mi.

    d.) Define an environment variable named GET_HOSTS_FROM and its value should be dns.

    e.) Container port should be Redis default port i.e 6379.

    Create another service named redis-slave. It should use Redis default port i.e 6379.

    Create another service named redis-follower. Port and targetPort should be Redis default port i.e 6379. Its selector app should be redis-slave.

FRONT END TIER

    Create a deployment named frontend.

    a.) Replicas count should be 3.

    b.) Container name should be php-redis-nautilus and it should use gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff image.

    c.) Request resources as CPU should be 100m and Memory should be 100Mi.

    d.) Define an environment variable named as GET_HOSTS_FROM and its value should be dns.

    e.) Container port should be 80.

    Create a service named frontend. Its type should be NodePort, port should be 80 and its nodePort should be 30009.

Finally, you can check the guestbook app by clicking on App button.

You can use any labels as per your choice.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

Looks like we have a lot to create again.  Let's start with the first deployment:

```bash
kubectl create deploy redis-master --replicas=1 --image=redis --port=6379 --dry-run=client -oyaml > redis-master.yaml
vim redis-master.yaml
```

Then start adding the specs:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis-master
  name: redis-master
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis-master
  strategy: {}
  template:
    metadata:
      labels:
        app: redis-master
    spec:
      containers:
      - image: redis
        name: master-redis-nautilus # don't forget to set this
        ports:
        - containerPort: 6379
        resources:
          requests:
            memory: "100Mi"
            cpu: "100m"
```

Apply that and then create the service:

```bash
kubectl apply -f redis-master.yaml
kubectl expose deploy redis-master --port=6379 --target-port=6379
```

Deployment 1 is finished.  Time for deployment 2:

```bash
kubectl create deploy redis-slave --replicas=2 --image=gcr.io/google_samples/gb-redisslave:v3 --port=6379 --dry-run=client -oyaml > redis-slave.yaml
vim redis-slave.yaml
```

And again all we really need to do is change the container name, add the resource requests and set the env variable:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: redis-slave
  name: redis-slave
spec:
  replicas: 2
  selector:
    matchLabels:
      app: redis-slave
  strategy: {}
  template:
    metadata:
      labels:
        app: redis-slave
    spec:
      containers:
      - image: gcr.io/google_samples/gb-redisslave:v3
        name: slave-redis-nautilus
        ports:
        - containerPort: 6379
        env:
          - name: GET_HOSTS_FROM
            value: dns
        resources:
          requests:
            memory: "100Mi"
            cpu: "100m"
```

Then we have to create two services for this per the task, but they'll both be selecting the same pods :

```bash
kubectl expose deploy redis-slave --port=6379 --dry-run=client -o yaml > network.yaml
vim network.yaml
```

Then we basically copy/paste the service it created, and change only the name of the second one to redis-follower

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: redis-slave
  name: redis-slave
spec:
  ports:
  - port: 6379
    protocol: TCP
    targetPort: 6379
  selector:
    app: redis-slave
status:
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: redis-slave
  name: redis-follower
spec:
  ports:
  - port: 6379
    protocol: TCP
    targetPort: 6379
  selector:
    app: redis-slave
status:
```

Apply that, and the backend tier will be done:

```bash
kubectl apply -f network.yaml
```

Then we just have to get the frontend deployment and service up.  Let's start with the deployment:

```bash
kubectl create deploy frontend --replicas=3 --image=gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff --port=80 --dry-run=client -oyaml > frontend.yaml
vim frontend.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: frontend
  name: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  strategy: {}
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - image: gcr.io/google-samples/gb-frontend@sha256:a908df8486ff66f2c4daa0d3d8a2fa09846a1fc8efd65649c0109695c7c5cbff
        name: php-redis-nautilus
        ports:
        - containerPort: 80
        env:
          - name: GET_HOSTS_FROM
            value: dns
        resources:
          requests:
            memory: "100Mi"
            cpu: "100m"
```

Apply it, then we're going to create the service template:

```bash
kubectl apply -f frontend.yaml
kubectl expose deploy frontend --type=NodePort --port=80 --dry-run=client -oyaml > frontend-network.yaml
vim frontend-network.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: frontend
  name: frontend
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30009 # add this
  selector:
    app: frontend
  type: NodePort
status:
  loadBalancer: {}
```

Apply it, and we should be good:

```bash
kubectl apply -f frontend-network.yaml
```

## Validation

Click the app button at the top, and we should see the Guesbook app fully functional.

## Insights

Another very long one with lots of things to wire together.

There's one thing that is a little bit tricky in the instructrions, and it's the two services pointing to the redis-slave pods.  There's really nothing wrong with pointing several services at the same set of pods, it just becomes different DNS names that resolve to the same backends.

Other than that, nothing was particularly difficult or complicated on its own.  I'm kind of sad that this is the final Kubernetes task for 100 days of devops because these have been the most fun by far.
