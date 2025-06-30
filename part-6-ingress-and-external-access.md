# 🚪 Part 6: Ingress and Network Policies

In this section, you'll learn how to expose services to the outside world using **Ingress** and how to secure Pod communication with **NetworkPolicies**.

---

## 🌐 Ingress in Minikube

Minikube includes a built-in **Ingress Controller** via an addon. You should have enabled it in Part 1. If not:

```bash
minikube addons enable ingress
```

---
### ✅ 1. Confirm the apps(frontend and backend) are deployed: (Step 3)
```bash
kubectl get po

```
Output should show all the pods running

```bash
```

---

### ✅ 2. Create Ingress Resource

We will expose the frontend via a DNS path.

```yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-nginx
  namespace: adv-net-lab
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
    - host: nginx.local
      http:
        paths:
          - path: /frontend
            pathType: Prefix
            backend:
              service:
                name: frontend
                port:
                  number: 80
          - path: /backend
            pathType: Prefix
            backend:
              service:
                name: backend
                port:
                  number: 8080


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

### ✅ Solution: Port forwarding
Port forward ingress service:

```bash
kubectl port-forward svc/ingress-nginx-controller -n ingress-nginx 8080:80
```
This means that your ingress will run on port 8080 on your local machine

Since we configured ingress to be accessible via host ```nginx.local```. We will need to map ```nginx.local``` to resolve to ```127.0.0.1```

```bash
echo "127.0.0.1 nginx.local" | sudo tee -a /etc/hosts
```
Use ping to make sure ```nginx.local``` is resolving to ```127.0.0.1```

```bash
PING nginx.local (127.0.0.1): 56 data bytes
64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.086 ms
```

Finally, open your browser to 
```bash
http://nginx.local:8080/frontend
```

You should be able to see the frontend page



---

### ✅ Ingress Summary

| Component         | Description                                   |
|------------------|-----------------------------------------------|
| Ingress Resource | Defines routes to Services based on domain/path |
| Ingress Controller | Watches Ingress resources and serves traffic |

---

➡️ [Continue to Part 7: Cleanup and Conclusion](part-7-cleanup-and-conclusion.md)
