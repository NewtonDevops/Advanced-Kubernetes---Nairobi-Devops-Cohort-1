# 🚀 Cilium with Observability on Minikube — Hands-on Lab

This guide walks you through installing **Cilium with Hubble** (observability UI), simulating traffic between pods, and visualizing it in the Hubble UI.

Open ChatGpt and search ```eli5 cni ebpf and cilium```

---

## 🧱 Step 1: Start Minikube Without a Default CNI

Start Minikube with Docker driver and no default CNI plugin:

```bash
minikube start --network-plugin=cni --cni=false --driver=docker
```

 ## Step 2: Install Cilium with Hubble Enabled

 Add the Cilium Helm repo and install Cilium with observability features:
 
 ```bash
helm repo add cilium https://helm.cilium.io/
helm repo update

helm install cilium cilium/cilium \
  --version 1.15.3 \
  --namespace kube-system \
  --set hubble.enabled=true \
  --set hubble.relay.enabled=true \
  --set hubble.ui.enabled=true \
  --set operator.replicas=1

```

## 🔍 Step 3: Verify Installation
Check that Cilium and Hubble components are running:
```bash
kubectl -n kube-system get pods -l k8s-app=cilium
kubectl -n kube-system get pods -l k8s-app=hubble-relay
kubectl -n kube-system get pods -l k8s-app=hubble-ui
```
## 🖥️ Step 4: Access Hubble UI
Port-forward the Hubble UI service:
```bash
kubectl port-forward -n kube-system svc/hubble-ui 12000:80

```

## 🌐 Step 5: Simulate Pod-to-Pod Traffic
Deploy an NGINX server and a busybox client:
```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80

kubectl run test-client --image=busybox --command -- sleep 3600

```
From the ```test-client``` pod, send a request to NGINX:
```bash
kubectl exec -it test-client -- wget -O- nginx
```
✅ Hubble will now show this network flow in the UI.

## 📊 Step 6: Observe Traffic in Hubble

Access http://localhost:12000 to view:

- Pod-to-pod connections

- Flow direction (ingress/egress)

- Allowed or denied status

- HTTP methods and DNS queries (L7 info)

Optional CLI observability:

```bash

cilium hubble enable
cilium hubble port-forward &
cilium hubble observe --follow
```

## 🔒 Step 7: Simulate Blocked Traffic with NetworkPolicy

Apply a deny-all ingress policy:

```yaml
# deny-all.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
  namespace: default
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```
Apply it:

```bash
kubectl apply -f deny-all.yaml
```
Try curling NGINX again:

```bash
kubectl exec -it test-client -- wget -O- nginx
```

❌ This should fail — check Hubble UI or CLI for "dropped" traffic.

✅ Cleanup
When done, remove the cluster:

```bash
minikube delete
```

---

## Read more
- (Cilium)[https://cilium.io/]
- (eBPF)[https://ebpf.io/]

