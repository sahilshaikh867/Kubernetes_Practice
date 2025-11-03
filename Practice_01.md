## ⚙️ Step 1: Install & Start Minikube

```bash
sudo apt update -y
sudo apt install -y curl apt-transport-https virtualbox
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

✅ **Start Minikube**

```bash
minikube start --driver=docker
```

Check status:

```bash
minikube status
```

---

## 🧰 Step 2: Install kubectl (if not done)

```bash
sudo snap install kubectl --classic
```

Check version:

```bash
kubectl version --client
```

---

## 🧩 Step 3: Create a Test Deployment

Let’s deploy a simple nginx app to test scaling.

```bash
kubectl create deployment nginx --image=nginx
```

Expose it as a service:

```bash
kubectl expose deployment nginx --port=80 --type=NodePort
```

Check everything:

```bash
kubectl get pods
kubectl get svc
```

---

## 📊 Step 4: Enable Metrics Server

Metrics Server collects CPU/memory data (needed for HPA).

```bash
minikube addons enable metrics-server
```

Then confirm it’s working:

```bash
kubectl get deployment metrics-server -n kube-system
```

---

## 📈 Step 5: Create HPA (Horizontal Pod Autoscaler)

Let’s tell Kubernetes to scale nginx between 1–5 replicas based on CPU load.

```bash
kubectl autoscale deployment nginx --cpu-percent=50 --min=1 --max=5
```

Check HPA status:

```bash
kubectl get hpa
```

---

## 🔥 Step 6: Generate Load to Trigger Scaling

Let’s create a busybox pod to spam the nginx service:

```bash
kubectl run -i --tty load-generator --image=busybox /bin/sh
```

Once inside:

```bash
while true; do wget -q -O- http://nginx.default.svc.cluster.local; done
```

🌀 Leave it running for a few minutes — then open another terminal and check:

```bash
kubectl get hpa
kubectl get pods
```

You should see the **replicas increase** automatically. 🚀

---

## 🧹 Step 7: Clean Up

Stop everything neatly:

```bash
kubectl delete deployment nginx
kubectl delete svc nginx
kubectl delete hpa nginx
minikube stop
```

---

## 🎯 Recap

✅ Created Minikube cluster
✅ Deployed nginx app
✅ Enabled metrics server
✅ Set up HPA to scale automatically
✅ Tested scaling under load

---
Be..Happy..! @sahilshaikh86767
