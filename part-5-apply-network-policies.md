## 🔒 Part 5: Apply Network Policies

Network Policies let you control which Pods can communicate with each other, enabling fine-grained security within your cluster.

By default, all traffic between Pods is allowed. Applying a `NetworkPolicy` restricts communication unless explicitly allowed.

---

### ✅ Step 1: Verify Current Connectivity
Ensure your Pods can communicate freely:
```bash
wget --timeout=2 backend:8080
```
✅ You should see a response like "Hello from backend"

---

### ✅ Step 2: Apply a Default Deny Policy
This will block all incoming traffic to Pods **unless explicitly allowed**.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
spec:
  podSelector: {} # targets all pods in the namespace.
  policyTypes:
  - Ingress # Applies to incoming traffic
```

```bash
kubectl apply -f default-deny.yaml
```

🔒 Now try reaching the backend again — it should fail:
```bash
wget --timeout=2 backend:8080
```

---

### ✅ Step 3: Allow Traffic from Frontend Only
Now create a rule that **only allows traffic from the frontend Pods** to the backend.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
  policyTypes:
  - Ingress
```

```bash
kubectl apply -f allow-frontend.yaml
```

✅ Retest from the frontend Pod:
```bash
kubectl exec deploy/frontend -- wget --timeout=2 backend:8080
```
✅ You should get a success response.

---

### Additional commands
List network policies
```bash
kubectl get networkpolicy -n <namespace>
```
Cluster wide

```bash
kubectl get networkpolicy --all-namespaces
```

### ✅ Summary Table

| Policy Type     | Description                            |
|-----------------|----------------------------------------|
| Default Allow   | No policies; all traffic allowed       |
| Default Deny    | Blocks all unless explicitly allowed   |
| Selective Allow | Enables access only from defined sources |

---

➡️ [Continue to Part 6: Ingress and External Access](part-6-ingress-and-external-access.md)
