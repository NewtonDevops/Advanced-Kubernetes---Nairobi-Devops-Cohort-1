## 🧪 Part 4: Explore Kubernetes Service Types

Kubernetes Services abstract access to Pods. This part explores how to expose applications using different types of services.

---

### 🔸 ClusterIP (default)
- Exposes the Service on a cluster-internal IP.
- Not accessible from outside the cluster.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-service
spec:
  type: ClusterIP
  selector:
    app: backend
  ports:
  - port: 8080
    targetPort: 8080
```

Apply and test inside a pod:
```bash
kubectl apply -f internal-service.yaml
kubectl run testbox --image=busybox --rm -it -- /bin/sh
wget -qO- internal-service:8080
```

---

### 🔸 NodePort
- Exposes the service on each Node’s IP at a static port (30000–32767).
- Accessible externally via `http://<NodeIP>:<NodePort>`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-service
spec:
  type: NodePort
  selector:
    app: backend
  ports:
  - port: 8080
    targetPort: 8080
    nodePort: 30080
```

Apply and test from your host machine:
```bash
kubectl apply -f nodeport-service.yaml
curl http://localhost:30080
```

---

### 🔸 LoadBalancer
- Creates an external IP using a cloud provider’s load balancer.
- On Docker Desktop, behaves like NodePort unless using something like MetalLB.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: lb-service
spec:
  type: LoadBalancer
  selector:
    app: backend
  ports:
  - port: 80
    targetPort: 8080
```

```bash
kubectl apply -f lb-service.yaml
kubectl get svc lb-service
```

Test using the EXTERNAL-IP:
```bash
curl http://<EXTERNAL-IP>
```

#### Learn more about setting up metalLB [here](https://medium.com/@shoaib_masood/metallb-network-loadbalancer-minikube-335d846dfdbe)
---

### 🔸 Headless Service
- Does not allocate a ClusterIP.
- Enables direct pod access via DNS.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: headless-backend
spec:
  clusterIP: None
  selector:
    app: backend
  ports:
  - port: 8080
    targetPort: 8080
```

Check DNS resolution:
```bash
kubectl run testbox --image=busybox --rm -it -- /bin/sh
nslookup headless-backend
```

---

### 📊 Summary Table

| Service Type | External Access | Use Case |
|--------------|------------------|----------|
| ClusterIP | ❌ Internal only | Default for internal communication |
| NodePort | ✅ Host IP on static port | Dev/testing environments |
| LoadBalancer | ✅ Cloud load balancing | Production in cloud providers |
| Headless | ❌/✅ (via Pod IPs) | Stateful apps, direct Pod comms |

---

➡️ [Continue to Part 5: Apply Network Policies](part-5-apply-network-policies.md)
