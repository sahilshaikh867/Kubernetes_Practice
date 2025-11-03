# 🚀 Minikube + HPA (Horizontal Pod Autoscaler) Setup Guide

This project documents the complete setup and configuration of **Minikube** and **Kubernetes HPA (Horizontal Pod Autoscaler)** for learning and practice purposes.  

It’s designed to help understand how containerized workloads scale automatically in Kubernetes, starting from the **local Minikube cluster** setup to **autoscaling deployments** based on real CPU load.

---

## 📘 Overview

- **Minikube** – a lightweight Kubernetes implementation that runs a cluster locally.  
- **HPA (Horizontal Pod Autoscaler)** – automatically adjusts the number of pods in a deployment based on observed CPU or memory usage.  
- **Docker** – used as the container runtime driver for Minikube.  

This repo serves as a foundation for:
- Learning how Kubernetes clusters behave on a local setup  
- Testing scaling scenarios without needing a cloud cluster  
- Experimenting with resource limits and metrics in Kubernetes  

---

## 🧠 Concepts Covered

| Topic | Description |
|-------|--------------|
| 🧩 **Minikube Setup** | Installing and configuring a local K8s cluster using Docker as the driver |
| ⚙️ **kubectl** | Command-line tool used to interact with the Kubernetes API |
| 📊 **Metrics Server** | Enables resource-based autoscaling by collecting pod metrics |
| 📈 **HPA** | Automatically scales deployments based on CPU usage |
| 🧹 **Cleanup** | Steps to stop and remove the Minikube cluster cleanly |

---

## 🧰 Tools Used

- **Ubuntu 24.04 LTS**
- **Docker v28.5.1**
- **kubectl v1.34.1**
- **Minikube v1.37.0**

---

## 🪄 Author Notes

> This guide is built for hands-on learners who want to master Kubernetes scaling concepts locally before deploying them on cloud platforms like AWS EKS or GKE.

Stay tuned for upcoming guides on:
- Ingress setup  
- Persistent volumes  
- Cluster monitoring with Prometheus + Grafana  

---

## 🧑‍💻 Author
**Sahil** — Engineering student at SPPU  
Learning DevOps | Exploring Cloud | Automating Everything ⚙️  

---

✨ _“Build small. Scale smart. Automate everything.”_
