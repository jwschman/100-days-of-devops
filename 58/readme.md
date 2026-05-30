# Day 58

## Task

The Nautilus DevOps teams is planning to set up a Grafana tool to collect and analyze analytics from some applications. They are planning to deploy it on Kubernetes cluster. Below you can find more details.

1.) Create a deployment named grafana-deployment-devops using any grafana image for Grafana app. Set other parameters as per your choice.

2.) Create NodePort type service with nodePort 32000 to expose the app.

You do not need to make any configuration changes inside the Grafana app once deployed; just make sure you can access the Grafana login page.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

Again, this can be done with a couple simple commands and then going in to the templates to add some parameters.  First, we'll create the deployment and service:

```bash
kubectl create deployment grafana-deployment-devops --image=grafana/grafana:latest
kubectl expose deployment grafana-deployment-devops --type=NodePort --port=3000 --name=grafana-service-devops --dry-run=client -oyaml > service.yaml
```

Then, we'll edit the service.yaml file to add the nodePort:

```bash
vim service.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: grafana-deployment-devops
  name: grafana-service-devops
spec:
  ports:
  - port: 3000
    protocol: TCP
    targetPort: 3000
    nodePort: 32000
  selector:
    app: grafana-deployment-devops
  type: NodePort
status:
  loadBalancer: {}
```

Finally, we'll apply the service:

```bash
kubectl apply -f service.yaml
```

## Validation

All we really need to do to check is click the Grafana tab at the top of the page, and it should be working

## Insights

Another quick one.  The deployment was created with just a single line, and the service only needed to have the `nodePort` parameter added before being applied.

Had I not done all the prep for the CKA using commands like this it would likely be more difficult because I don't actually use these commands in my daily use.
