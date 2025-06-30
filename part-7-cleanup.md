## 🧹 Part 7: Cleanup

Now that you’ve explored and configured advanced networking in Kubernetes, it’s time to clean up the resources and wrap up.

---

### 🧼 Cleanup: Delete All Created Resources (manually created)

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

### If deployed via helm chart

```bash
helm uninstall demo
```
