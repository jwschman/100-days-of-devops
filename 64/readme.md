# Day 64

## Task

One of the DevOps engineers was trying to deploy a python app on Kubernetes cluster. Unfortunately, due to some mis-configuration, the application is not coming up. Please take a look into it and fix the issues. Application should be accessible on the specified nodePort.

    The deployment name is python-deployment-nautilus, its using poroko/flask-demo-app image. The deployment and service of this app is already deployed.

    nodePort should be 32345 and targetPort should be python flask app's default port.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

Well first let's check the status of the pod:

```bash
kubectl get pods
```

It shows `ImagePullBackoff` so let's go in and fix that:

```bash
kubectl edit deploy p[ython-deploymentnautilus
```

```yaml
    spec:
      containers:
      - image: poroko/flask-demo-app # changed from poroko/flask-app-demo
```

Then check if it's running:

```bash
kubectl get pods
```

But if we click the App button at the top we get a 502 error, so there's probably something wrong with the service as well:

```bash
kubectl get svc
```

We can see our `python-service-nautilus` of type `NodePort`, but it's port (`8080:32345`) may be incorrect, so let's check what the deployment actually uses:

```bash
kubectl describe deploy python-deployment-nautilus
```

And there we can see: `Port: 5000/TCP` so we edit the service to use port 5000:

```bash
kubectl edit svc python-service-nautilus
```

```yaml
  ports:
  - nodePort: 32345
    port: 5000 # changed from 8080
    protocol: TCP
    targetPort: 5000 # also changed from 8080
```

## Validation

Click the App button again at the top and we should see: "Hello World Pyvo 1!"

## Insights

This one was much quicker after the previous one.  Just an easy to miss image name mismatch, and fixing a port.  Finding the port was possibly the most difficult part, but that was just looking it up with `kubectl describe deploy python-deployment-nautilus` and pulling it from there.
