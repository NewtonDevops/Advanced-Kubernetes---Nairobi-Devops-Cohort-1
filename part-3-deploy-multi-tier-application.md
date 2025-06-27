## ⚙️ Part 3: Deploy Multi-Tier Application

With the networking foundation covered, let's deploy a simple multi-tier application to see Kubernetes networking in action.
### Architecture
```mermaid
flowchart LR
    subgraph Outside[Outside World]
      User[User / Browser]
    end

    User -- HTTP/HTTPS --> Ingress[Ingress Controller]
    Ingress -- routes --> svcFrontend[Service: frontend]
    svcFrontend -- selects --> podFront1[(Pod)]
    svcFrontend -- selects --> podFront2[(Pod)]
    podFront1 -- REST --> svcBackend[Service: backend]
    podFront2 -- REST --> svcBackend
    svcBackend -- selects --> podBack1[(Pod)]
    svcBackend -- selects --> podBack2[(Pod)]

    classDef svc fill:#a2d9ff,stroke:#0366d6,stroke-width:1px,color:#000;
    classDef pod fill:#c0f5d9,stroke:#22863a,stroke-width:1px,color:#000;

    class svcFrontend,svcBackend svc;
    class podFront1,podFront2,podBack1,podBack2 pod;
```

External traffic enters through **Ingress**, reaches a **Service** which load‑balances to healthy **Pods**.  Internal calls cascade through additional Services.  **NetworkPolicies** restrict who can talk to whom, and the **CNI plugin** wires the packets

This setup includes:
- A **backend** pod responding on HTTP
- A **frontend** pod (nginx) acting as a consumer
- A **test pod** to simulate internal access

---

### ✅ Step 1: Create the Backend Deployment and Service

```yaml
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

### 🔍 Inspect Logs or Describe a Pod

To view logs from a Pod:
```bash
kubectl logs <pod-name> -n adv-net-lab
```

To describe the details of a Pod:
```bash
kubectl describe pod <pod-name> -n adv-net-lab
```
This shows metadata, events, container status, restart counts, and reasons for failures.

---
To rollout restart a deployment
```bash
kubectl rollout restart deploy <deployment-name>
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
