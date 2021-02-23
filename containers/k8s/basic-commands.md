# K8S Helpsheet

## Setup KUBECONFIG
>Need to make to make sure you **kubectl** installed

You need to have your KUBECONFIG exported.

**Linux**:
```
export KUBECONFIG={your_kubeconfig_path}
```

**Windows PowerShell**:
```
Set KUBECONFIG={your_kubeconfig_path}
```

---

## Basic commands

Checking k8s basic resource types:
```
kubectl get {nodes/deployments/services/ingress/pods}
```
If you want to see all the pods that the system is running then use:
```
kubectl get {nodes/deployments/services/ingress/pods} --all-namespaces
```
To create a resource type, you create a yaml file of type `{resource_type}` then run this command to apply it:
```
kubectl create -f ./ingress.yml
kubectl create -f path/to/file.yml
```
To edit a resource:
```
kubectl edit ingress app-ingress
kubectl edit service service-one
kubectl edit {resource_type} {resource_name}
```

---

## Resource types yaml file examples
### Deployment:
This allows you to get a service going.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: service-one
  labels:
    app: service-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: service-one
  template:
    metadata:
      labels:
        app: service-one
    spec:
      containers:
      - name: service-one
        image: service_one
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 4000
```
> `imagePullPolicy: IfNotPresent`: allows you to provide a local image <br>
> `containerPort: 4000`: container port must map to port exposed by service

### Deployment (for nginx reverse proxy):

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: service-reverse
  labels:
    app: service-reverse
spec:
  replicas: 1
  selector:
    matchLabels:
      app: service-reverse
  template:
    metadata:
      labels:
        app: service-reverse
    spec:
      containers:
      - name: service-reverse
        image: nginx
        volumes:
          - ./reverse-proxy/nginx.conf:/etc/nginx/nginx.conf
        ports:
        - containerPort: 80
```

### Ingress:
> See create-ingress.md