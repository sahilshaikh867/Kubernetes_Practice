# 🚀 Minikube + HPA Hands-On Guide (Styled & Step-by-step)

> **Goal:** Install and start Minikube on an Ubuntu machine (Docker backend), fix the `RSRC_INSUFFICIENT_CORES` issue, deploy a sample app (NGINX), enable metrics, and configure Horizontal Pod Autoscaler (HPA).  
> This file contains each command with an explanation and helpful tips — ready to push to your GitHub.

---

## 🧰 Prerequisites (Quick checklist)
- Docker installed and running. (`docker --version`)  
- `kubectl` installed. (`kubectl version --client`)  
- Minikube binary downloaded. (`minikube version`)  
- At least **2 CPU cores available** on the host (Minikube requires >=2).  
- Recommended: **4 GB RAM** for smooth experience.

---

## 🔍 Why you saw this error?
```
⛔  Exiting due to RSRC_INSUFFICIENT_CORES:  has less than 2 CPUs available, but Kubernetes requires at least 2 to be available
```
Minikube requires **at least 2 CPU cores** available to run Kubernetes components (control plane + kubelet). On small cloud instances (like t2.micro / t3.nano) only 1 vCPU is available — that's the cause.

---

## ✅ Step 0 — Quick host checks
Check number of CPUs available:
```bash
nproc
# or
grep -c ^processor /proc/cpuinfo
```
Check memory:
```bash
free -h
```
Check Docker running:
```bash
docker --version
sudo systemctl status docker
```

**If `nproc` returns `1`**, you need to either:
- Resize your EC2 instance to one with >= 2 vCPUs (recommended), **OR**
- Use another local machine with >= 2 cores, **OR**
- Use a different local Kubernetes solution (like `kind`) — but for this guide we'll continue with Minikube.

---

## 🛠️ Option A — Resize your EC2 instance (Recommended for cloud)
If you're on AWS EC2, stop the instance and change instance type to one with >=2 vCPUs (for example `t3.small`, `t3.medium`, `t3a.small`, etc.), then start the instance again.

---

## 🛠️ Option B — If you *do* have 2+ cores but Minikube still fails
Start minikube explicitly requesting resources (if host has them):
```bash
minikube start --driver=docker --cpus=2 --memory=4096
```
> `--cpus=2` requests 2 CPUs for the Minikube VM/container. `--memory=4096` assigns 4GB RAM.

If you run into permission / docker sock issues, avoid `chmod 777 /var/run/docker.sock` (it's insecure). Instead ensure your user is in the `docker` group:
```bash
sudo usermod -aG docker $USER
# then either:
newgrp docker
# or log out and log back in
```

---

## 📥 Install (if needed) — commands you already ran (for reference)
### kubectl
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```

### Minikube
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version
```

---

## 🚀 Start Minikube (correctly)
If host has >=2 CPUs:
```bash
minikube start --driver=docker --cpus=2 --memory=4096
```
**What this does:**
- Uses Docker driver (creates minikube control-plane inside a Docker container)
- Allocates 2 CPUs and 4GB RAM to Minikube

If you get the RSRC error, increase your host CPU count (resize instance) or use a machine with more cores.

---

## 🔎 Verify cluster & context
```bash
kubectl get nodes
kubectl cluster-info
kubectl config current-context
```

Expected:
- `kubectl get nodes` should show `minikube` in `Ready` state.

---

## 🧩 Deploy a sample app (NGINX)
Create a deployment with 1 replica:
```bash
kubectl create deployment nginx-deploy --image=nginx --replicas=1
kubectl expose deployment nginx-deploy --type=NodePort --port=80
kubectl get pods
kubectl get svc
```

Open the service in your browser (minikube will forward or show URL):
```bash
minikube service nginx-deploy --url
# or
minikube service nginx-deploy
```

---

## ⚙️ Enable metrics-server (required for HPA)
HPA needs metrics; enable Minikube's addon:
```bash
minikube addons enable metrics-server
# Check:
kubectl get pods -n kube-system | grep metrics
```

If metrics-server pod isn't ready, wait a minute and re-check.

---

## 📈 Create Horizontal Pod Autoscaler (HPA)
Autoscale `nginx-deploy` based on CPU usage (target 50% CPU, min 1 pod, max 5 pods):
```bash
kubectl autoscale deployment nginx-deploy --cpu-percent=50 --min=1 --max=5
kubectl get hpa
```

HPA shows current CPU usage and desired replicas.

---

## 🧪 Simulate load to trigger HPA
Run a load-generator (busybox) pod to hit NGINX repeatedly:
```bash
kubectl run -i --tty load-generator --image=busybox --restart=Never -- /bin/sh
# then inside the shell:
while true; do wget -q -O- http://nginx-deploy.default.svc.cluster.local; done
```
Open another terminal and monitor:
```bash
kubectl get hpa -w
kubectl get pods -w
```
You should see replicas increase when CPU crosses the threshold.

To stop load generator: `Ctrl+C` in that shell, then:
```bash
kubectl delete pod load-generator
```

---

## 🧽 Cleanup (when you’re done)
```bash
kubectl delete hpa nginx-deploy
kubectl delete service nginx-deploy
kubectl delete deployment nginx-deploy
minikube stop
minikube delete
```

---

## ⚠️ Troubleshooting tips & notes
- **RSRC_INSUFFICIENT_CORES** → Resize instance or use a host with >=2 CPUs. Minikube *requires* 2+.
- **Docker permission errors** → add your user to `docker` group instead of `chmod 777`:
  ```bash
  sudo usermod -aG docker $USER; newgrp docker
  ```
- **metrics-server not ready** → check logs:
  ```bash
  kubectl -n kube-system logs deploy/metrics-server
  ```
- **If using cloud VM**: prefer resizing instance type vs trying `--driver=none`. `--driver=none` runs components on host directly and can be messier.

---

## 📝 Extra: Useful commands cheat-sheet
```bash
# Status
minikube status
kubectl get nodes,pods,svc,deploy

# Logs
kubectl logs deploy/nginx-deploy
kubectl describe hpa nginx-deploy

# Port-forward (if you don't want minikube service)
kubectl port-forward svc/nginx-deploy 8080:80
# then access http://localhost:8080
```

---

## 🎯 Final tips (TL;DR)
- Check `nproc` first. If it's 1, increase host CPU count.  
- Use `minikube start --driver=docker --cpus=2 --memory=4096` when possible.  
- Enable metrics-server before creating HPA.  
- Use load-generator (busybox) to test autoscaling.

---

Happy to convert this into a prettier README with badges, or split it into separate `minikube.md` and `hpa.md`. Want that? 😎
