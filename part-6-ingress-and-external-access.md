# 🚪 Part 6: Ingress and Network Policies

In this section, you'll learn how to expose services to the outside world using **Ingress** and how to secure Pod communication with **NetworkPolicies**.

---

## 🌐 Ingress in Minikube

Minikube includes a built-in **Ingress Controller** via an addon. You should have enabled it in Part 1. If not:

```bash
minikube addons enable ingress
```

---
### ✅ 1. Deploy a Sample App
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:stable
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
```

```bash
kubectl apply -f nginx-deployment.yaml
```

---

### ✅ 2. Create Ingress Resource

We will expose the frontend via a DNS path.

```yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: nginx.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80

```

Apply it:
```bash
kubectl apply -f ingress.yaml
```

---

## Confirm the ingress

```bash
kubectl get svc ingress-nginx-controller -n ingress-nginx
```

Output should be similar to

```bash
NAME                       TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller   NodePort   10.103.59.66   <none>        80:32002/TCP,443:30866/TCP   13m
```
By default, Minikube installs the ingress-nginx-controller as a NodePort service, which:
- Does not assign an EXTERNAL-IP
- Exposes high-numbered ports (like 30000–32767) instead of port 80
- Requires curl ``` http://<minikube-ip>:<node-port>``` — which your browser/DNS won't use

## 💡 Why Does This Happen?

Kubernetes relies on a **cloud provider** or **load balancer controller** to allocate an external IP address for `LoadBalancer`-type services.

In local environments like Minikube, this functionality is not available out-of-the-box.  
To simulate it, Minikube provides a special routing process using:

### ✅ Solution: Patch the Service + Run Tunnel
Convert the Ingress controller's service to LoadBalancer:

```bash
kubectl patch svc ingress-nginx-controller -n ingress-nginx \
  -p '{"spec": {"type": "LoadBalancer"}}'
```
Then, in a separate terminal window, start the Minikube tunnel:

```bash
minikube tunnel
```

---

### ✅ Ingress Summary

| Component         | Description                                   |
|------------------|-----------------------------------------------|
| Ingress Resource | Defines routes to Services based on domain/path |
| Ingress Controller | Watches Ingress resources and serves traffic |

---

➡️ [Continue to Part 7: Cleanup and Conclusion](part-7-cleanup-and-conclusion.md)
