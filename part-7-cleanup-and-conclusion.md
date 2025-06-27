## 🧹 Part 7: Cleanup and Conclusion

Now that you’ve explored and configured advanced networking in Kubernetes, it’s time to clean up the resources and wrap up.

---

### 🧼 Cleanup: Delete All Created Resources

To remove all the resources created during this lab:
```bash
kubectl delete namespace adv-net-lab
```

Or, if you want to delete selectively:
```bash
kubectl delete deployment frontend backend
kubectl delete service frontend backend internal-service nodeport-service lb-service headless-backend
kubectl delete ingress frontend-ingress
kubectl delete networkpolicy deny-all allow-frontend-to-backend
```

Also remove any test pods (if still running):
```bash
kubectl delete pod testbox --ignore-not-found
```

---

### 🎓 What You’ve Learned

✅ Core networking components (Pods, Services, CNI, NetworkPolicies)  
✅ Service exposure types (ClusterIP, NodePort, LoadBalancer, Headless)  
✅ Use and enforcement of Network Policies for secure communication  
✅ Configuring Ingress to expose apps via URLs or domains

---

### 📚 Next Steps

- Explore CNI plugins like Calico or Cilium in Minikube/kind
- Add TLS to your Ingress
- Use cert-manager to automate certificate provisioning
- Try service mesh tools like Istio or Linkerd

---

👏 Great work completing this advanced Kubernetes networking lab!

Want more labs like this? Star or fork this repo and try contributing!
