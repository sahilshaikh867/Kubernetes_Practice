### LET’S BUILD 🔥

**End-to-End Kubernetes Mini Project (Helm-based)**


> *“I’ve deployed a Helm-based app on Kubernetes.”*

---

# 🏗️ PROJECT OVERVIEW (WHAT WE’RE BUILDING)

### 🎯 Goal

Deploy a **web application** on Kubernetes using **Helm**, with:

* Deployment
* Service
* Ingress
* ConfigMap
* Secret
* Values-based configuration
* Upgrade & rollback support

### 🧠 Stack

* App: **Nginx (static site)**
* Container: Docker
* Orchestration: Kubernetes
* Packaging: **Helm**
* Networking: Service + Ingress

---

# 📁 PROJECT STRUCTURE (VERY IMPORTANT)

```text
k8s-helm-project/
├── app/
│   └── index.html
├── Dockerfile
├── myapp-chart/
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
│       ├── deployment.yaml
│       ├── service.yaml
│       ├── ingress.yaml
│       ├── configmap.yaml
│       └── secret.yaml
```

---

# 🧱 STEP 1: APPLICATION

### `index.html`

```html
<h1>Hello from Helm + Kubernetes 🚀</h1>
<p>Version: {{ .Values.app.version }}</p>
```

---

# 🐳 STEP 2: DOCKERFILE

```dockerfile
FROM nginx:alpine
COPY app/index.html /usr/share/nginx/html/index.html
```

Build image:

```bash
docker build -t myapp:1.0 .
```

(For real prod: push to Docker Hub / ECR)

---

# 🎁 STEP 3: CREATE HELM CHART

```bash
helm create myapp-chart
```

Remove default templates you don’t need.

---

# 📜 STEP 4: Chart.yaml

```yaml
apiVersion: v2
name: myapp
description: Helm-based Kubernetes app
version: 0.1.0
appVersion: "1.0"
```

---

# ⚙️ STEP 5: values.yaml (CONTROL CENTER)

```yaml
replicaCount: 2

image:
  repository: myapp
  tag: "1.0"

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: true
  host: myapp.local

app:
  version: "1.0"
```

👉 **Never hardcode values in templates. Values file is king.**

---

# 📦 STEP 6: Deployment Template

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app: {{ .Release.Name }}
    spec:
      containers:
      - name: nginx
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
        ports:
        - containerPort: 80
```

---

# 🌐 STEP 7: Service Template

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ .Release.Name }}
spec:
  type: {{ .Values.service.type }}
  selector:
    app: {{ .Release.Name }}
  ports:
  - port: {{ .Values.service.port }}
    targetPort: 80
```

---

# 🌍 STEP 8: Ingress Template

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: {{ .Release.Name }}
spec:
  rules:
  - host: {{ .Values.ingress.host }}
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: {{ .Release.Name }}
            port:
              number: 80
```

---

# 🧠 STEP 9: INSTALL USING HELM

```bash
helm install myapp ./myapp-chart
```

Verify:

```bash
helm list
kubectl get pods
kubectl get svc
kubectl get ingress
```

Add host entry:

```text
MINIKUBE-IP  myapp.local
```

Test:

```bash
curl http://myapp.local
```

🎉 App is LIVE.

---

# 🔄 STEP 10: UPGRADE (REAL POWER)

Change in `values.yaml`:

```yaml
replicaCount: 3
app:
  version: "2.0"
```

Upgrade:

```bash
helm upgrade myapp ./myapp-chart
```

Check:

```bash
kubectl get pods
```

---

# ♻️ STEP 11: ROLLBACK (INTERVIEW GOLD)

```bash
helm history myapp
helm rollback myapp 1
```

👉 Production safety net.

---

# 🧠 WHAT YOU JUST BUILT (IMPORTANT)

You demonstrated:

* Helm chart creation
* values-driven config
* Kubernetes deployments
* Ingress routing
* Upgrade & rollback
* Real DevOps mindset

This is **NOT beginner**.
This is **junior DevOps / cloud engineer level**.

---

# 🏆 RESUME-READY DESCRIPTION

> Deployed a Helm-based Kubernetes application with configurable values, Ingress routing, service abstraction, and release versioning. Implemented upgrade and rollback strategies using Helm.

Use this. Trust me.
