# Day 63

## Task

There is an iron gallery app that the Nautilus DevOps team was developing. They have recently customized the app and are going to deploy the same on the Kubernetes cluster. Below you can find more details:

    Create a namespace iron-namespace-xfusion

    Create a deployment iron-gallery-deployment-xfusion for iron gallery under the same namespace you created.

    :- Labels run should be iron-gallery.

    :- Replicas count should be 1.

    :- Selector's matchLabels run should be iron-gallery.

    :- Template labels run should be iron-gallery under metadata.

    :- The container should be named as iron-gallery-container-xfusion, use kodekloud/irongallery:2.0 image ( use exact image name / tag ).

    :- Resources limits for memory should be 100Mi and for CPU should be 50m.

    :- First volumeMount name should be config, its mountPath should be /usr/share/nginx/html/data.

    :- Second volumeMount name should be images, its mountPath should be /usr/share/nginx/html/uploads.

    :- First volume name should be config and give it emptyDir and second volume name should be images, also give it emptyDir.

    Create a deployment iron-db-deployment-xfusion for iron db under the same namespace.

    :- Labels db should be mariadb.

    :- Replicas count should be 1.

    :- Selector's matchLabels db should be mariadb.

    :- Template labels db should be mariadb under metadata.

    :- The container name should be iron-db-container-xfusion, use kodekloud/irondb:2.0 image ( use exact image name / tag ).

    :- Define ironment, set MYSQL_DATABASE its value should be database_blog, set MYSQL_ROOT_PASSWORD and MYSQL_PASSWORD value should be with some complex passwords for DB connections, and MYSQL_USER value should be any custom user ( except root ).

    :- Volume mount name should be db and its mountPath should be /var/lib/mysql. Volume name should be db and give it an emptyDir.

    Create a service for iron db which should be named iron-db-service-xfusion under the same namespace. Configure spec as selector's db should be mariadb. Protocol should be TCP, port and targetPort should be 3306 and its type should be ClusterIP.

    Create a service for iron gallery which should be named iron-gallery-service-xfusion under the same namespace. Configure spec as selector's run should be iron-gallery. Protocol should be TCP, port and targetPort should be 80, nodePort should be 32678 and its type should be NodePort.

Note:

    We don't need to make connection b/w database and front-end now, if the installation page is coming up it should be enough for now.

    The kubectl utility on the jump-host has been configured to work with the Kubernetes cluster.

## Solution

Another long one... let's just get working on it.  First create the namespace, then the deployment template and go in to edit it:

```bash
kubectl create ns iron-namespace-xfusion
kubectl create deploy iron-gallery-deployment-xfusion -n iron-namespace-xfusion --replicas=1 --image=kodekloud/irongallery:2.0 --dry-run=client -oyaml > iron-gallery.yaml
vim iron-gallery.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: iron-gallery
  name: iron-gallery-deployment-xfusion
  namespace: iron-namespace-xfusion
spec:
  replicas: 1
  selector:
    matchLabels:
      run: iron-gallery
  strategy: {}
  template:
    metadata:
      labels:
        run: iron-gallery
    spec:
      containers:
      - image: kodekloud/irongallery:2.0
        name: iron-gallery-container-xfusion
        resources:
          limits:
              memory: "100Mi"
              cpu: "50m"
        volumeMounts:
        - name: config
          mountPath: /usr/share/nginx/html/data
        - name: images
          mountPath: /usr/share/nginx/html/uploads
      volumes:
      - name: config
        emptyDir: {}
      - name: images
        emptyDir: {}
```

I used this page for resource limits: <https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/>

Then just use the same deployment template and edit for the second deployment:

```bash
cp iron-gallery.yaml iron-db.yaml
vim iron-db.yaml
```

And just match the spec from the task:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    db: mariadb
  name: iron-db-deployment-xfusion
  namespace: iron-namespace-xfusion
spec:
  replicas: 1
  selector:
    matchLabels:
      db: mariadb
  strategy: {}
  template:
    metadata:
      labels:
        db: mariadb
    spec:
      containers:
      - image: kodekloud/irondb:2.0
        name: iron-db-container-xfusion
        env:
        - name: MYSQL_DATABASE
          value: "database_blog"
        - name: MYSQL_ROOT_PASSWORD 
          value: "A-Very_complex_p455w0rd"
        - name: MYSQL_PASSWORD
          value: "Anoth3r_pAs5w0RD!"
        - name: MYSQL_USER
          value: "any_custom_user" #heh
        volumeMounts:
        - name: db
          mountPath: /var/lib/mysql
      volumes:
      - name: db
        emptyDir: {}
```

Then we create the services:

```bash
kubectl expose deploy -n iron-namespace-xfusion iron-gallery-deployment-xfusion --type=ClusterIP --port=3306 --dry-run=client -oyaml > network.yaml
vim network.yaml
```

Go in and edit it and copy/paste the second service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: iron-db-service-xfusion
  namespace: iron-namespace-xfusion
spec:
  ports:
  - port: 3306
    protocol: TCP
    targetPort: 3306
  selector:
    db: mariadb
  type: ClusterIP
---
apiVersion: v1
kind: Service
metadata:
  name: iron-gallery-service-xfusion
  namespace: iron-namespace-xfusion
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 32678
  selector:
    run: iron-gallery
  type: NodePort
```

## Validation

Make sure everything is running and that the services exist:

```bash
kubectl get pods -n iron-namespace-xfusion
kubectl get svc -n iron-namespace-xfusion
```

If they look good, we can click the App button at the top and hopefully see the installation page

## Insights

Nothing on this was really different than any of the other Kubernetes tasks so a lot could be copy/pasted.  There were just a lot of pieces for the task to be complete.

What kind of got me at the end was that I didn't notice that the second service for the gallery was supposed to be `type: NodePort` so the first time I clicked the App button I got a 502 error.  But then I went back into my network manifest, reread the spec, and noticed the problem.
