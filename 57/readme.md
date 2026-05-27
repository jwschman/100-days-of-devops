# Day 57

## Task

The Nautilus DevOps team is working on to setup some pre-requisites for an application that will send the greetings to different users. There is a sample deployment, that needs to be tested. Below is a scenario which needs to be configured on Kubernetes cluster. Please find below more details about it.

    Create a pod named print-envars-greeting.

    Configure spec as, the container name should be print-env-container and use bash image.

    Create three environment variables:

    a. GREETING and its value should be Welcome to

    b. COMPANY and its value should be Nautilus

    c. GROUP and its value should be Group

    Use command ["/bin/sh", "-c", 'echo "$(GREETING) $(COMPANY) $(GROUP)"'] (please use this exact command), also set its restartPolicy policy to Never to avoid crash loop back.

    You can check the output using kubectl logs -f print-envars-greeting command.

Note: The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

So we're just going to create this with a `kubectl` command and edit the template again:

```bash
kubectl run print-envars-greeting --image=bash --dry-run=client -oyaml > pod.yaml
```

Then we just add the environment vars, restartPolicy, and command to the pod template:

```bash
vim pod.yaml
```

I'm looking at this page on the docs for env: <https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/>

And this page for the command: <https://kubernetes.io/docs/tasks/inject-data-application/define-command-argument-container/>

In the end it should look like:

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: print-envars-greeting
  name: print-envars-greeting
spec:
  containers:
  - image: bash
    name: print-env-container
    resources: {}
    env:
    - name: GREETING
      value: Welcome to
    - name: COMPANY
      value: Nautilus
    - name: GROUP
      value: Group
    command: ["/bin/sh", "-c", 'echo "$(GREETING) $(COMPANY) $(GROUP)"']
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

## Validation

```bash
kubectl logs -f print-envars-greeting 
```

We should see:

`Welcome to Nautilus Group`

## Insights

I made a mistake again here... I forgot to rename the pod `print-env-container` like it asked in the task.  Oops.

Everything worked the second time though when I actually set the container name correctly.

I'm pretty used to setting env variables for containers and use it very often in my homelab.  One thing that I do a lot that this task didn't ask was reference secrets for the env variables rather than just defining them directly in the manifest.
