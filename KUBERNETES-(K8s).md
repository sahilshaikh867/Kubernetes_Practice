
**KUBERNETES (K8s)** 
---

# ☸️ KUBERNETES — FROM ZERO TO SOLID

## 🧠 WHY KUBERNETES EXISTS (HONEST ANSWER)

Docker alone can:

* Run containers ❌
* Auto-heal ❌
* Auto-scale ❌
* Load-balance ❌

Kubernetes solves:

> **How do we run containers reliably at scale?**

---

## 🧩 CORE KUBERNETES CONCEPTS (NON-NEGOTIABLE)

| Term       | Meaning                       |
| ---------- | ----------------------------- |
| Cluster    | Group of machines             |
| Node       | Single machine (VM)           |
| Pod        | Smallest unit (1+ containers) |
| Deployment | Manages Pods                  |
| Service    | Exposes Pods                  |
| Namespace  | Logical isolation             |

👉 **Pod ≠ Container** (very important)

---

## 🧠 K8s ARCHITECTURE (HIGH LEVEL)

### Control Plane

* API Server
* Scheduler
* Controller Manager
* etcd (cluster brain 🧠)

### Worker Node

* kubelet
* kube-proxy
* container runtime

---

## 🛠️ TOOLS YOU USE

```bash
kubectl   # talk to cluster
minikube # local cluster
```

Check:

```bash
kubectl version --client
```

---

## 🚀 FIRST K8s HANDS-ON (NO YAML YET)

### Create deployment

```bash
kubectl create deployment web --image=nginx
```

Check:

```bash
kubectl get pods
kubectl get deployments
```

---

### Expose deployment

```bash
kubectl expose deployment web --type=NodePort --port=80
```

Check service:

```bash
kubectl get svc
```

---

### Access app

```bash
minikube service web
```

🎉 Nginx running on Kubernetes.

---

## 🔄 SCALING (THIS IS K8s MAGIC)

```bash
kubectl scale deployment web --replicas=3
```

Check:

```bash
kubectl get pods
```

👉 Pods auto-created. No manual run.

---

## ♻️ SELF-HEALING DEMO

```bash
kubectl delete pod <pod-name>
```

Check again:

```bash
kubectl get pods
```

👉 Kubernetes brings it back automatically 😈

---

## 📄 YAML (NOW WE INTRODUCE IT)

### Basic Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

Apply:

```bash
kubectl apply -f web.yaml
```

---

## 🌐 SERVICES (VERY IMPORTANT)

| Type         | Use      |
| ------------ | -------- |
| ClusterIP    | Internal |
| NodePort     | Dev/Test |
| LoadBalancer | Cloud    |

---

## 🧠 INTERVIEW GOLD LINES

* Pod is smallest deployable unit
* Deployment ensures desired state
* Service provides stable networking
* K8s = declarative system

---

## ⚠️ COMMON K8s MISTAKES

❌ Editing running Pods
❌ Forgetting labels
❌ Using NodePort in prod

---

## 🧪 MINI LAB (MANDATORY)

1️⃣ Create deployment
2️⃣ Scale to 3 pods
3️⃣ Delete one pod
4️⃣ Watch auto-heal

If this feels logical → you’re K8s-ready.

---

## 🏁 KUBERNETES STATUS

You now understand:

* Why K8s exists
* Core objects
* Scaling + healing
* YAML basics

This is **strong Kubernetes foundation**.

---
