## 🌐 Part 6: Ingress and External Access

Kubernetes **Ingress** allows you to expose HTTP and HTTPS routes from outside the cluster to Services inside the cluster. It's a smart router for your Kubernetes apps.

> Requires an **Ingress Controller** (e.g., NGINX) to be installed in the cluster.

---

### ✅ 1. Install Ingress Controller (if not already)

If you haven't installed it yet:
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.1/deploy/static/provider/cloud/deploy.yaml
```

Wait for the controller to be ready:
```bash
kubectl get pods -n ingress-nginx -w
```

---

### ✅ 2. Create Ingress Resource

We will expose the frontend via a DNS path.

```yaml
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: frontend-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /frontend
        pathType: Prefix
        backend:
          service:
            name: frontend
            port:
              number: 80
```

Apply it:
```bash
kubectl apply -f ingress.yaml
```

---

### ✅ 3. Test Ingress Access

Get the Ingress controller's external IP:
```bash
kubectl get svc -n ingress-nginx ingress-nginx-controller
```

Assuming `localhost`, test it:
```bash
curl http://localhost/frontend
```

✅ You should receive an NGINX welcome page or error (depending on frontend setup).

---

### ✅ 4. Bonus: Custom Domain (Optional)

To use a custom domain (e.g., `myapp.local`):

1. Add to `/etc/hosts`:
   ```bash
   127.0.0.1 myapp.local
   ```
2. Update `ingress.yaml`:
   ```yaml
   rules:
   - host: myapp.local
     http:
       paths:
       - path: /
         pathType: Prefix
         backend:
           service:
             name: frontend
             port:
               number: 80
   ```
3. Access it:
   ```bash
   curl http://myapp.local/
   ```

---

### ✅ Ingress Summary

| Component         | Description                                   |
|------------------|-----------------------------------------------|
| Ingress Resource | Defines routes to Services based on domain/path |
| Ingress Controller | Watches Ingress resources and serves traffic |

---

➡️ [Continue to Part 7: Cleanup and Conclusion](part-7-cleanup-and-conclusion.md)
