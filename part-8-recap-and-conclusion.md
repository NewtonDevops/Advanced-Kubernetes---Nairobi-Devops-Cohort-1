# 📋 Recap: Commands & Best Practices

This document summarizes all the key commands and best practices covered in the course for quick reference.

---

## 🧰 Environment Setup

```bash
# Start Minikube
minikube start --driver=docker

# Enable Ingress controller
minikube addons enable ingress

# Set namespace for the course
kubectl create namespace adv-net-lab
kubectl config set-context --current --namespace=adv-net-lab

## 🔧 Core kubectl Commands
```bash
# View current context and namespace
kubectl config current-context
kubectl config view --minify

# List all resources in namespace
kubectl get all -n adv-net-lab

# View pod labels
kubectl get pods --show-labels -n adv-net-lab

# Logs and debugging
kubectl logs <pod-name>
kubectl describe pod <pod-name>

# Execute a shell inside a pod
kubectl exec -it <pod-name> -- sh

# Restart a deployment
kubectl rollout restart deployment <name>

# Port forward to access service locally
kubectl port-forward svc/<service-name> <local-port>:<service-port> -n <namespace>

```

## 🚀 Docker + Minikube
``` bash
# Use Minikube Docker daemon
eval $(minikube docker-env)

# Build image and load into Minikube
docker build -t <image-name>:<version/tag> .

```

## 🌐 Ingress Controller (with Port Forwarding)
Minikube exposes the Ingress controller via a NodePort service by default.

``` bash
# Get the port number (typically 30000+ range)
kubectl get svc ingress-nginx-controller -n ingress-nginx
```
Example output:

``` pgsql
NAME                       TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller   NodePort   10.103.59.66   <none>        80:32002/TCP,443:30866/TCP   13m
```

To access Ingress from your browser or curl:

``` bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8080:80
```

Then access:
```bash
curl -H "Host: nginx.local" http://localhost:8080
```

💡 You can also map nginx.local to 127.0.0.1 in /etc/hosts for browser access.

## 🧪 Network Testing

``` bash
# Inside a test pod
nslookup backend
wget -qO- backend:8080
```

## 📦 Helm Commands
``` bash
# Create a Helm chart
helm create <chart-name>

# Install or upgrade release
helm install demo ./multi-tier-app -n adv-net-lab
helm upgrade demo ./multi-tier-app -n adv-net-lab

# Uninstall a Helm release
helm uninstall demo -n adv-net-lab

# Add/update dependencies
helm dependency build
```

---

✅ Best Practices
- Always set a namespace and work within it e.g ```(adv-net-lab)```.

- Use ```imagePullPolicy: Never``` when loading local images in Minikube.

- Use port forwarding to access Ingress and services without relying on ```EXTERNAL-IP```.

- Ensure correct labels match between the Deployment and Service.

- Use Helm for templating, reusability, and environment-specific configs.

- Use ```NetworkPolicies``` to explicitly allow/deny pod communication.

- Use descriptive annotations and ```pathType: Prefix``` in Ingress.

- Keep ```Chart.yaml```, ```values.yaml```, and dependencies (Chart.lock) in sync.


---



