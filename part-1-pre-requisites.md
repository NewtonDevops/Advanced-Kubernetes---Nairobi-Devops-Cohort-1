# 🚧 Part 1: Pre-requisites (Minikube Edition)

Before diving into Kubernetes networking, ensure you have the right tools and setup. This section helps you prepare your environment to run all upcoming examples using **Minikube**.

---

## ✅ Required Tools

| Tool                  | Purpose                         | Install Link                                                |
|-----------------------|----------------------------------|--------------------------------------------------------------|
| Minikube              | Local Kubernetes cluster         | https://minikube.sigs.k8s.io/docs/start/                     |
| kubectl               | CLI to interact with Kubernetes  | https://kubernetes.io/docs/tasks/tools/#kubectl             |
| YAML Editor           | For manifest files (e.g. VS Code)| https://code.visualstudio.com/                              |
| curl / wget           | Test HTTP endpoints              | Usually preinstalled                                         |
| K9s or Lens (optional)| Kubernetes UI tool               | https://k9scli.io/ or https://k8slens.dev/                  |

---

## ✅ Start Minikube

Install and start your local Kubernetes cluster:

```bash
  minikube start --cni=calico --memory=4g --cpus=2 # use calico cni for Networking
```

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

### ✅ Enable Ingress (to be used in part 6)

```bash
minikube addons enable ingress
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
