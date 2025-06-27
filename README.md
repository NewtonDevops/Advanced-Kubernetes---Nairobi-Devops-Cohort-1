# 🧭 Advanced Kubernetes Networking Lab

Welcome to the **Advanced Kubernetes Networking Lab**.  
This guide walks you through deploying and securing multi‑tier applications while deep‑diving into real‑world **Kubernetes networking** tools and concepts.

---

## 🌟 Kubernetes Basics — Quick Recap

Before we jump into advanced networking, here’s a 60‑second refresher on the core building blocks you’ll be working with:

| Building Block | What It Is | Why It Matters |
|----------------|------------|----------------|
| **Cluster** | The entire Kubernetes control‑plane + worker nodes | Runs and manages all workloads |
| **Node** | A single VM/host inside the cluster | Provides CPU / memory where Pods run |
| **Pod** | Smallest deployable unit that wraps one or more containers sharing the same network namespace | Gets its **own IP** and is the primary target selected by Services |
| **Deployment** | Declarative controller that ensures the desired number of Pod replicas | Handles rolling updates & self‑healing |
| **Service** | Stable virtual IP + DNS name that load‑balances traffic to a set of Pods | Decouples clients from changing Pod IPs |
| **Ingress** | L7 HTTP/HTTPS router that brings external traffic into Services | Single entry point with path / host‑based routing |
| **NetworkPolicy** | Firewall rules for Pod‑to‑Pod (and Pod‑to‑world) traffic | Enables zero‑trust micro‑segmentation |
| **CNI Plugin** | Low‑level network provider that gives Pods IPs & enforces policies | Under the hood wiring for all traffic |

### 📈 Big‑Picture Diagram

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

*Legend*: External traffic enters through **Ingress**, reaches a **Service** which load‑balances to healthy **Pods**.  Internal calls cascade through additional Services.  **NetworkPolicies** (not shown) restrict who can talk to whom, and the **CNI plugin** wires the packets.

---

## 📘 Outline

Each section builds upon the last, so it's best to follow them in order:

1. [Part 1: Pre-requisites](part-1-pre-requisites.md)
2. [Part 2: Kubernetes Networking Concepts](part-2-kubernetes-networking-concepts.md)
3. [Part 3: Deploy Multi-Tier Application](part-3-deploy-multi-tier-application.md)
4. [Part 4: Explore Kubernetes Service Types](part-4-explore-kubernetes-service-types.md)
5. [Part 5: Apply Network Policies](part-5-apply-network-policies.md)
6. [Part 6: Ingress and External Access](part-6-ingress-and-external-access.md)
7. [Part 7: Cleanup and Conclusion](part-7-cleanup-and-conclusion.md)
