# Tutorial:  Hello World Deployed on a GKE Cluster

This guides you from having the YAML files to verifying the app
running through an Ingress.\
It is designed for students and beginners working with GKE (or any
Kubernetes cluster with an Ingress Controller).

---

## Prerequisites

-   `kubectl` configured and pointing to your cluster (e.g.,
    `gcloud container clusters get-credentials ...`).
-   An Ingress Controller installed (GKE uses the GCE controller by
    default;.
-   Files: `deployment.yaml`, `service.yaml`, `ingress.yaml`.

---

## 1) YAML Files (Minimal Example)

Save these files in the same directory.

### `deployment.yaml`

``` yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: helloworld-deployment
  labels:
    app: helloworld
spec:
  replicas: 3
  selector:
    matchLabels:
      app: helloworld
  template:
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: helloworld
        image: gcr.io/google-samples/hello-app:1.0
        ports:
        - containerPort: 8080 
```

### `service.yaml`

``` yaml
apiVersion: v1
kind: Service
metadata:
  name: helloworld-service
spec:
  selector:
    app: helloworld
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: ClusterIP 
```

### `ingress.yaml` (GKE Example)

``` yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: helloworld-ingress
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: helloworld-service
            port:
              number: 80 
```

---

## 2) Apply the Manifests

``` bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml
```

---

## 3) Verify Resources

### Deployments

``` bash
kubectl get deployment
```

Expected output:

``` bash
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment   3/3     3            3           5m
```

### Services

``` bash
kubectl get service
```

Example:

``` bash
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
helloworld-service   ClusterIP   10.12.34.56     <none>        80/TCP    5m
``` 

### Ingress

``` bash
kubectl get ingress
```

Example:

``` bash
NAME                 CLASS    HOSTS   ADDRESS         PORTS   AGE
helloworld-ingress   <none>   *       35.186.210.90   80      5m
```

`ADDRESS` is the Load Balancer IP. It may take a few minutes to appear.

---

## 4) Test the Application

``` bash
curl http://<EXTERNAL-IP>/
```

Example:

``` bash
curl http://35.186.210.90/
Hello, world!
Version: 1.0.0
Hostname: helloworld-deployment-67884bdb54-f8pcf
```

---

## 5) Quick Debugging

Check pods:

``` bash
kubectl get pods -l app=helloworld
kubectl describe pod <POD_NAME>
kubectl logs <POD_NAME>
```

Check Ingress & Service:

``` bash
kubectl describe ingress helloworld-ingress
kubectl describe svc helloworld-service
```

Port-forward for local testing:

``` bash
kubectl port-forward deployment/helloworld-deployment 8080:8080
curl http://localhost:8080/
```

---

## 6) Cleanup

``` bash
kubectl delete -f ingress.yaml
kubectl delete -f service.yaml
kubectl delete -f deployment.yaml
```

or:

``` bash
kubectl delete -f .
```
