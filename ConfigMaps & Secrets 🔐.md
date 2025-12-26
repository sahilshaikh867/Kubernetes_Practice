
**ConfigMaps & Secrets** — this is where Kubernetes stops being “toy clusters” and starts looking like **production**.

---

# 🔐 CONFIGMAPS & SECRETS (DEEP + PRACTICAL)

## 🧠 WHY THESE EXIST (REAL REASON)

Hard-coding values inside images is **bad practice**.

Problems:

* Same image for dev/prod? ❌
* Change config → rebuild image? ❌
* Secrets in Git? 🚫🚫

K8s says:

> **Separate code from configuration**

---

# 📦 CONFIGMAPS

## 🔹 What is a ConfigMap?

> Stores **non-sensitive configuration data**

Examples:

* App port
* ENV name
* Feature flags
* URLs

---

## 🔹 CREATE CONFIGMAP (CLI WAY)

```bash
kubectl create configmap app-config \
  --from-literal=APP_ENV=prod \
  --from-literal=APP_PORT=8080
```

Check:

```bash
kubectl get configmap
kubectl describe configmap app-config
```

---

## 🔹 USE CONFIGMAP AS ENV VARIABLES

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-pod
spec:
  containers:
  - name: app
    image: nginx
    env:
    - name: APP_ENV
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: APP_ENV
```

Apply:

```bash
kubectl apply -f cm-pod.yaml
```

---

## 🔹 USE CONFIGMAP AS FILE (VERY COMMON)

```yaml
volumes:
- name: config-vol
  configMap:
    name: app-config
```

Mounted inside container as files.

---

# 🔐 SECRETS

## 🔹 What is a Secret?

> Stores **sensitive data**

Examples:

* Passwords
* Tokens
* API keys

⚠️ Secrets are **base64 encoded**, not encrypted by default.

---

## 🔹 CREATE SECRET (CLI)

```bash
kubectl create secret generic db-secret \
  --from-literal=DB_USER=admin \
  --from-literal=DB_PASS=admin123
```

Check:

```bash
kubectl get secrets
kubectl describe secret db-secret
```

---

## 🔹 USE SECRET AS ENV

```yaml
env:
- name: DB_USER
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: DB_USER
```

---

## 🔹 USE SECRET AS FILE

```yaml
volumes:
- name: secret-vol
  secret:
    secretName: db-secret
```

---

# 🧠 CONFIGMAP vs SECRET (INTERVIEW FAVORITE)

| ConfigMap              | Secret         |
| ---------------------- | -------------- |
| Non-sensitive          | Sensitive      |
| Plain text             | Base64 encoded |
| Safe for Git (usually) | Never commit   |

---

## ⚠️ COMMON MISTAKES (VERY IMPORTANT)

❌ Putting secrets in ConfigMaps
❌ Thinking secrets are encrypted
❌ Forgetting pod restart after update

---

## 🧪 MINI LAB (DO THIS)

1️⃣ Create ConfigMap
2️⃣ Inject as env
3️⃣ Create Secret
4️⃣ Inject as env
5️⃣ Describe pod and verify

If you can explain this → you’re **production aware**.

---

## 🏁 STATUS CHECK

You now understand:

* Why config & secrets are separate
* How to create & use both
* ENV vs volume mounting

This is **mid-level Kubernetes knowledge** 🔥
