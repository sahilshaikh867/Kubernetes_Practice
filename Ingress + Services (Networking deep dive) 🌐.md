
**Ingress + Services — Networking Deep Dive**
This is where Kubernetes stops being “it runs” and becomes **“users can actually reach it”**.
We’ll go layer by layer, no confusion, no magic words.

---

# 🧠 BIG PICTURE (LOCK THIS FIRST)

**Pod** → ephemeral, dies, IP changes
**Service** → stable access inside/outside cluster
**Ingress** → smart HTTP/HTTPS gateway (URLs, domains, SSL)

> **Ingress does NOT replace Service. It sits ON TOP of it.**

---

# 🧱 KUBERNETES SERVICE (FOUNDATION)

## ❓ Why Service exists?

Pods:

* Are temporary
* Get new IPs when recreated

Service:

> **Provides a stable virtual IP + DNS name**

---

## 🔹 SERVICE TYPES (VERY IMPORTANT)

### 1️⃣ ClusterIP (Default)

* Internal only
* Pod ↔ Pod communication

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-svc
spec:
  type: ClusterIP
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 80
```

Use case: backend APIs, DB access

---

### 2️⃣ NodePort

* Exposes service on each node IP
* Port range: `30000–32767`

```yaml
type: NodePort
```

Access:

```
http://NODE-IP:PORT
```

✅ Good for dev/testing
❌ Not for production

---

### 3️⃣ LoadBalancer

* Cloud only (AWS/GCP/Azure)
* Creates external LB automatically

```yaml
type: LoadBalancer
```

Access:

```
http://EXTERNAL-IP
```

✅ Production-friendly
❌ Costs money

---

## 🧠 SERVICE FLOW (MENTAL MAP)

```
User → Service → Pod
```

Service load-balances traffic to all matching Pods.

---

# 🌐 INGRESS (THE REAL GATEWAY)

## ❓ Why Ingress exists?

Problems without Ingress:

* One LoadBalancer per app (💸)
* No URL-based routing
* No TLS termination

Ingress solves:

> **One entry point → many services**

---

## 🔑 IMPORTANT TRUTH

> **Ingress is useless without an Ingress Controller**

Examples:

* NGINX Ingress Controller
* Traefik
* HAProxy

---

## 🚀 INGRESS CONTROLLER (MINIKUBE)

```bash
minikube addons enable ingress
```

Check:

```bash
kubectl get pods -n ingress-nginx
```

---

# 📄 BASIC INGRESS EXAMPLE

### Service already exists: `web-svc`

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-ingress
spec:
  rules:
  - host: web.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-svc
            port:
              number: 80
```

Apply:

```bash
kubectl apply -f ingress.yaml
```

---

## 🧪 TEST LOCALLY (VERY IMPORTANT)

Edit `/etc/hosts`:

```text
MINIKUBE-IP  web.local
```

Test:

```bash
curl http://web.local
```

🎉 You’re using Ingress.

---

# 🔀 MULTIPLE APPS WITH ONE INGRESS (REAL USE)

```yaml
rules:
- host: app1.local
  http:
    paths:
    - path: /
      backend:
        service:
          name: app1-svc
          port:
            number: 80

- host: app2.local
  http:
    paths:
    - path: /
      backend:
        service:
          name: app2-svc
          port:
            number: 80
```

👉 One IP, multiple apps. This is the **killer feature**.

---

# 🔐 INGRESS + TLS (HTTPS)

```yaml
tls:
- hosts:
  - web.local
  secretName: web-tls
```

Ingress handles SSL, pods stay HTTP. Clean.

---

# 🧠 SERVICE vs INGRESS (INTERVIEW GOLD)

| Service       | Ingress           |
| ------------- | ----------------- |
| L4 / basic L7 | L7 (HTTP/HTTPS)   |
| Stable IP     | Smart routing     |
| Pod exposure  | User-facing entry |
| Required      | Optional          |

**One-liner:**

> Service exposes Pods, Ingress exposes Services.

---

# ⚠️ COMMON MISTAKES (PROD KILLERS)

❌ No Ingress controller installed
❌ Expecting Ingress without Service
❌ Using NodePort in prod
❌ Forgetting DNS / hosts entry

---

# 🧪 MINI LAB (MANDATORY)

1️⃣ Create Deployment
2️⃣ Create ClusterIP Service
3️⃣ Enable Ingress
4️⃣ Create Ingress rule
5️⃣ Access app via hostname

If this makes sense → **you understand K8s networking** 💪

---

# 🏁 STATUS CHECK

You now know:

* How traffic flows in K8s
* Service types and use cases
* Ingress routing & TLS
* Production vs dev patterns

This is **real Kubernetes knowledge**, not surface-level.

