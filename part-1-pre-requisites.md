## 🚧 Part 1: Pre-requisites

Before diving into Kubernetes networking, ensure you have the right tools and setup. This section helps you prepare your environment to run all upcoming examples.

### ✅ Required Tools

| Tool | Purpose | Install Link |
|------|---------|--------------|
| Docker Desktop | Local Kubernetes cluster | https://www.docker.com/products/docker-desktop |
| kubectl | CLI to interact with Kubernetes | https://kubernetes.io/docs/tasks/tools/#kubectl |
| YAML Editor | For manifest files (e.g. VS Code) | https://code.visualstudio.com/ |
| curl / wget | Test HTTP endpoints | Usually preinstalled |
| K9s or Lens (optional) | Kubernetes UI tool | https://k9scli.io/ or https://k8slens.dev/ |

### ✅ Enable Kubernetes on Docker Desktop
1. Open Docker Desktop
2. Go to **Settings > Kubernetes**
3. Enable the **"Enable Kubernetes"** checkbox
4. Apply and restart

Verify installation:
```bash
kubectl cluster-info
kubectl get nodes
```

### ✅ Set a Namespace for the Lab
```bash
kubectl create namespace adv-net-lab
kubectl config set-context --current --namespace=adv-net-lab
```


### ✅ DNS/Network Tools (Optional but helpful)
```bash
# macOS
brew install curl wget httpie

# Ubuntu/Debian
sudo apt install curl wget netcat dnsutils
```

### ✅ (Optional) Install Ingress Controller (to be used in part 6)
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.1/deploy/static/provider/cloud/deploy.yaml
```

Wait until it's ready:
```bash
kubectl get pods -n ingress-nginx -w
```

### ✅ Useful commands:

```bash
# more details per pod
kubectl get pods -n adv-net-lab -o wide
```

To check the current context:
```bash
kubectl config current-context
kubectl config view --minify
```

To list all available contexts:
```bash
kubectl config get-contexts
```

### 📊 Check Pod Status
Get the status of all Pods in the lab namespace:
```bash
kubectl get pods -n adv-net-lab
```

Or view them with more detail:
```bash
kubectl get pods -n adv-net-lab -o wide
```

✅ Done with setup!

➡️ [Continue to Part 2: Kubernetes Networking Concepts](part-2-kubernetes-networking-concepts.md)
