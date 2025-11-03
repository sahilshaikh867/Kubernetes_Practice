# 💥 Minikube + HPA Practical Sheet

## 🧩 Objective

Set up a **local Kubernetes cluster using Minikube**, deploy an **Nginx app**, and configure **Horizontal Pod Autoscaler (HPA)** to scale pods automatically based on CPU usage.

---

## ⚙️ Environment Setup

### 1️⃣ Update System

```bash
sudo apt update -y
```

### 2️⃣ Install Docker

If Docker isn’t installed or working properly:

```bash
sudo apt install ca-certificates curl gnupg lsb-release -y
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo tee /etc/apt/keyrings/docker.asc > /dev/null
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update -y
sudo apt install docker-ce docker-ce-cli containerd.io -y
sudo systemctl enable --now docker
```

✅ **Check Docker:**

```bash
docker --version
sudo systemctl status docker
```

---

## 🚀 Install Minikube + kubectl

### 3️⃣ Install kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```

### 4️⃣ Install Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

---

## 🧠 Start Minikube Cluster

### 5️⃣ Start with Docker Driver

> You **must have at least 2 CPUs & 4GB RAM** available.

```bash
minikube start --driver=docker --cpus=2 --memory=4096
```

✅ **Verify:**

```bash
minikube status
kubectl get nodes
kubectl cluster-info
```

If any error says “insufficient CPU”, upgrade instance (e.g., `t3.medium` in AWS).

---

## 🧱 Deploy Nginx

### 6️⃣ Create Deployment

```bash
kubectl create deployment nginx --image=nginx
```

### 7️⃣ Expose Deployment

```bash
kubectl expose deployment nginx --port=80 --type=NodePort
```

### 8️⃣ Check Pods & Services

```bash
kubectl get pods
kubectl get svc
```

---

## 📊 Enable Metrics Server

### 9️⃣ Turn on Metrics

```bash
minikube addons enable metrics-server
```

Check if it’s running:

```bash
kubectl get deployment metrics-server -n kube-system
```

---

## ⚖️ Configure Horizontal Pod Autoscaler (HPA)

### 🔟 Create HPA

```bash
kubectl autoscale deployment nginx --cpu=50% --min=1 --max=5
```

### 1️⃣1️⃣ Verify HPA

```bash
kubectl get hpa
```

---

## 🔥 Generate Load

### 1️⃣2️⃣ Stress Test Nginx

Open another terminal and run:

```bash
kubectl run -it --rm load-generator --image=busybox /bin/sh
```

Then inside the shell:

```bash
while true; do wget -q -O- http://nginx.default.svc.cluster.local; done
```

Watch pods scale up:

```bash
kubectl get hpa
kubectl get pods -w
```

---

## 🧹 Cleanup

```bash
kubectl delete hpa nginx
kubectl delete svc nginx
kubectl delete deployment nginx
minikube stop
minikube delete
```

---

## 🧰 Troubleshooting

| Problem                    | Fix                                                 |
| -------------------------- | --------------------------------------------------- |
| ❌ Minikube not starting    | Use `--cpus=2 --memory=4096` or larger EC2 instance |
| ❌ `localhost:8080 refused` | Cluster not running — check `minikube status`       |
| ❌ No metrics data          | Wait ~1-2 mins for metrics-server to collect data   |
| ❌ Docker conflict          | Remove old Docker with `sudo apt remove docker.io`  |

---

## 🎯 Final Check

✅ Docker installed
✅ Minikube cluster running
✅ Nginx deployed
✅ Metrics server active
✅ HPA scaling pods automatically

---

## ✨ Author

**Sahil** — Engineering Student @ SPPU
💻 DevOps Learner | ☁️ Cloud Enthusiast | 🧩 Automating Everything

> *"Don’t just deploy — scale smart."* 🚀

---
Bye...! Sahil Shaikh
