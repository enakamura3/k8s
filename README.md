# k8s

- [k8s](#k8s)
  - [Getting Started](#getting-started)
  - [Templates](#templates)
    - [Deployment](#deployment)
    - [Service](#service)
  - [Sample application](#sample-application)
    - [mongodb](#mongodb)
    - [mongodb-express](#mongodb-express)
    - [test](#test)

## Getting Started

To use on your local machine, install `minikube`!

> https://minikube.sigs.k8s.io/docs/start/

Or use in some cloud provider you like.

## Templates

We can use kubectl to create some templates to fill with values.

### Deployment

```sh
kubectl create deployment sample --image hello-world --dry-run -o yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: sample
  name: sample
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sample
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: sample
    spec:
      containers:
      - image: hello-world
        name: hello-world
        resources: {}
status: {}
```

### Service

```sh
kubectl create service clusterip sample-svc --tcp=27017:27017 --dry-run -o yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: sample-svc
  name: sample-svc
spec:
  ports:
  - name: 27017-27017
    port: 27017
    protocol: TCP
    targetPort: 27017
  selector:
    app: sample-svc
  type: ClusterIP
status:
  loadBalancer: {}
```

## Sample application

Sample project using __mongodb__ and __mongodb-express__ to show some k8s compoenents use.

Content from: [TechWorld with Nana](https://www.youtube.com/watch?v=X48VuDVv0do)

Create the namespace __mongodb__

```sh
kubectl create namespace mongo
```

### mongodb

Execute the commands following the order:

```sh
kubectl apply -f mongodb-configmap.yaml
kubectl apply -f mongodb-secret.yaml
kubectl apply -f mongodb-deploy.yaml
kubectl apply -f mongodb-svc.yaml
```

### mongodb-express

Execute the commands following the order:

```sh
kubectl apply -f mongo-express-deploy.yaml
kubectl apply -f mongo-express-svc-external.yaml
```

### test

Execute the command to see the external IP

```sh
minikube service list

|----------------------|---------------------------|--------------------|---------------------------|
|      NAMESPACE       |           NAME            |    TARGET PORT     |            URL            |
|----------------------|---------------------------|--------------------|---------------------------|
| default              | kubernetes                | No node port       |
| kube-system          | kube-dns                  | No node port       |
| kubernetes-dashboard | dashboard-metrics-scraper | No node port       |
| kubernetes-dashboard | kubernetes-dashboard      | No node port       |
| mongo                | mongodb-svc               | No node port       |
| mongo                | mongoexpress-svc          | tcp/8081           | http://192.168.49.2:30000 |
|----------------------|---------------------------|--------------------|---------------------------|
```

The IP address in use is from the node:

```sh
kubectl get node -o wide
NAME       STATUS   ROLES                  AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION       CONTAINER-RUNTIME
minikube   Ready    control-plane,master   15d   v1.20.2   192.168.49.2   <none>        Ubuntu 20.04.1 LTS   5.8.0-7642-generic   docker://20.10.2
```

Access the URL: 

> http://192.168.49.2:30000