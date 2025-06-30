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
NAME                             READY   STATUS    RESTARTS   AGE
backend-78b696fbd4-4bddp         1/1     Running   0          81m
frontend-764f75976d-jjhq2        1/1     Running   0          58m
testbox-allow-597c5559b8-7sgvf   1/1     Running   0          55m
testbox-deny-75d68bf895-75qx9    1/1     Running   0          54m
```

---

### ✅ 2. Create Ingress Resource

We will expose the frontend via a DNS path.

```yaml

apiVersion: networking.k8s.io/v1  # Specifies the Ingress API version
kind: Ingress                     # Defines the resource kind as Ingress
metadata:
  name: ingress-nginx            # Name of the Ingress resource
  namespace: adv-net-lab         # Namespace where this Ingress is deployed
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /  # Rewrites incoming request paths to /
spec:
  ingressClassName: nginx        # IngressClass to use (NGINX controller)
  rules:
    - host: nginx.local          # Hostname for the Ingress (e.g. nginx.local in /etc/hosts)
      http:
        paths:
          - path: /frontend      # Route traffic from /frontend path
            pathType: Prefix     # Match all paths that start with /frontend
            backend:
              service:
                name: frontend   # Forward to the frontend service
                port:
                  number: 80     # On port 80 of the service

          - path: /backend       # Route traffic from /backend path
            pathType: Prefix     # Match all paths that start with /backend
            backend:
              service:
                name: backend    # Forward to the backend service
                port:
                  number: 8080   # On port 8080 of the service



```

🧩 Key Components
Field	Description

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
