## 🧠 Part 2: Kubernetes Networking Concepts

Kubernetes networking is built on a few core concepts that ensure connectivity and scalability across distributed workloads. Here's what you need to know before working with networking in the cluster:

### 🧱 Pods
- The smallest deployable unit in Kubernetes
- One or more containers sharing the same network namespace
- All containers in a Pod communicate via `localhost`

### 🌍 Services
- Abstract layer to expose a group of Pods
- Provide a stable DNS name and internal IP
- Handles load balancing within the group of Pods

Types of Services:
- `ClusterIP`: Accessible only within the cluster
- `NodePort`: Exposes service on each node at a static port
- `LoadBalancer`: Creates an external IP (cloud)
- `Headless`: No ClusterIP, direct Pod DNS resolution

### 🔐 NetworkPolicy
- Used to control traffic between Pods
- Default: all Pods can talk to each other
- Once a policy exists, only allowed traffic is permitted
- Useful for Zero Trust security model

### 🧩 CNI (Container Network Interface)
- A plugin system for pod networking
- Assigns IP addresses to Pods
- Sets up routes, interfaces, and enforces policies

Popular CNIs:
- Calico
- Flannel
- Cilium
- Weave

### 🔄 How It All Works Together
1. Kubernetes schedules a Pod
2. CNI assigns a unique IP
3. A Service provides access to the Pod
4. NetworkPolicies define who can access the Pod
5. DNS and routing ensure packets arrive properly

---

✅ You're now ready to deploy applications and observe how networking behaves in action.

➡️ [Continue to Part 3: Deploy Multi-Tier Application](part-3-deploy-multi-tier-application.md)
