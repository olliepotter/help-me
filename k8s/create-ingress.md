
# Setup k8s ingress

## Setup ingress controller
Basically, there is a resource called **Ingress**. You can set one up and configure one but it is useless unless you have an **Ingress Controller**.

When using k8s with docker you don't have an ingress setup by default so to set one up you just run the command:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.44.0/deploy/static/provider/cloud/deploy.yaml
```

This goes and grabs the deployment file for the ingress controller from an offical k8s repo.

> This link was found at `https://kubernetes.github.io/ingress-nginx/deploy/`

## Setup an ingress
Now we have an ingress controller we can create an ingress and it will act on it.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
  - host: kubernetes.docker.internal
    http:
        paths:
          - path: /service-one(/|$)(.*)
            pathType: Prefix
            backend:
              service:
                name: service-one
                port:
                  number: 4000
          - path: /service-two(/|$)(.*)
            pathType: Prefix
            backend:
              service:
                name: service-two
                port:
                  number: 4002
```

This creates an ingress called `app-ingress`. This ingress registers two routes `/service-one` and `/service-two` and hosts them on `kubernetes.docker.internal`.

So to access services: **service-one** you would do:
```
http://kubernetes.docker.internal/service-one
```
This would put you at the root of service-one. It is not the default behaviour to forward to route... I had to configure the ingress using this article:
`https://kubernetes.github.io/ingress-nginx/examples/rewrite/`

Effectively, any characters captured by **(.*)** will be assigned to the placeholder $2, which is then used as a parameter in the rewrite-target annotation.

> Captured groups are saved in numbered placeholders, chronologically, in the form $1, $2 ... $n. These placeholders can be used as parameters in the rewrite-target annotation.

---

Just for context `kubectl get services`:
```
NAME          TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP          3h1m
service-one   NodePort    10.105.157.83   <none>        4000:32104/TCP   165m
service-two   NodePort    10.102.12.113   <none>        4002:32717/TCP   162m
```