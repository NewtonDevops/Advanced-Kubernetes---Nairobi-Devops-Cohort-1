## ⚙️ Part 3: Deploy Multi-Tier Application

With the networking foundation covered, let's deploy a simple multi-tier application to see Kubernetes networking in action.

This setup includes:
- A **backend** pod responding on HTTP
- A **frontend** pod (nginx) acting as a consumer
- A **test pod** to simulate internal access

---

### ✅ Step 1: Create the Backend Deployment and Service

```yaml
# backend.yaml
apiVersion: v1
kind: Service
metadata:
  name: backend
spec:
  selector:
    app: backend
  ports:
  - port: 8080
    targetPort: 8080
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: hashicorp/http-echo
        args: ["-text=Hello from backend"]
        ports:
        - containerPort: 8080
```

Apply it:
```bash
kubectl apply -f backend.yaml
```

---

### ✅ Step 2: Create the Frontend Deployment and Service

```yaml
# frontend.yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: nginx
        ports:
        - containerPort: 80
```

Apply it:
```bash
kubectl apply -f frontend.yaml
```

---

### ✅ Step 3: Test Internal Communication

Use a temporary test Pod to simulate traffic:
```bash
kubectl run testbox --image=busybox --rm -it -- /bin/sh
```
Inside the testbox shell:
```sh
nslookup backend
wget -qO- backend:8080
```

✅ You should see: `Hello from backend`

---

➡️ [Continue to Part 4: Explore Kubernetes Service Types](part-4-explore-kubernetes-service-types.md)
