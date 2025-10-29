#  Kubernetes for MLOps and DevOps in Enterprise Applications

This repository provides a **complete guide and hands-on walkthrough** of how **Kubernetes (K8s)** powers **DevOps** and **MLOps** workflows for enterprise-scale applications.

Whether you are a **ML Engineer**, **Data Scientist**, or **DevOps practitioner**, this guide will help you understand:
- How Kubernetes works  
- The role of YAML files  
- How clusters are provisoined  
- How containers, pods, and services interact  
- How to deploy apps for **DevOps CI/CD pipelines** and **MLOps workloads**

---

##  Table of Contents

1. [What is Kubernetes?](#-1-what-kubernetes-is)
2. [What is a Cluster?](#-2-what-a-kubernetes-cluster-is)
3. [What Do YAML Files Do?](#-3-what-the-yaml-files-do)
4. [Installing Kubernetes (Minikube)](#-4-do-you-need-to-install-kubernetes-before-writing-yaml)
5. [What Happens When You Deploy YAML?](#-5-what-happens-when-you-deploy-yaml)
6. [Common Kubernetes Objects](#-6-key-yaml-object-types-youll-see)
7. [Kubernetes in DevOps](#-7-kubernetes-in-devops)
8. [Kubernetes in MLOps](#-8-kubernetes-in-mlops)
9. [Hands-On Example — Deploying NGINX](#-hands-on-example--deploy-nginx-locally)
10. [Cleanup](#-step-8-clean-up)
11. [Final Mental Model](#-final-mental-model)
12. [Next Steps](#-next-step-optional)

---

##  1. What Kubernetes *Is*

Kubernetes (often abbreviated as **K8s**) is an **orchestration platform** for containers.

> You have multiple containers (Docker images) that you want to run reliably across several machines. Kubernetes is the *manager* that ensures they stay healthy, available, and scalable.

### Kubernetes handles:
- Container **scheduling** and **placement**
- **Self-healing** (restarts crashed containers)
- **Scaling** (up/down automatically)
- **Load balancing**
- **Networking** between services
- **Rolling updates** without downtime

---

##  2. What a Kubernetes Cluster Is

A Kubernetes **cluster** is made up of two main parts:

| Component | Description |
|------------|--------------|
| **Control Plane** | The *brain* that makes global decisions (scheduling, scaling, etc.). |
| **Worker Nodes** | The *muscles* that actually run your containers. |

Think of it as:
> Control Plane = Manager  
> Worker Nodes = Employees doing the actual work

---

##  3. What the YAML Files Do

Kubernetes uses **YAML files** as blueprints to describe what you want it to create or manage.

These are **declarative** — meaning you describe the *desired state*, and Kubernetes figures out *how* to reach it.

---

###  Example: Simple Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        image: nginx:latest
        ports:
        - containerPort: 80
```

When you run:

```python
kubectl apply -f my-webapp.yaml
```

##### Kubernetes ensures that 3 pods are running an NGINX container.

##### If one fails, it spins up another automatically.

--- 

## 4. Do You Need to Install Kubernetes Before Writing YAML?

##### You can write YAML files anytime (they’re just text), but to execute them, you need a running Kubernetes cluster.

# Options for Clusters

| Type        | Description |
|-------------|-------------|
| **Local**   | Use Minikube or Kind for testing/development. |
| **Cloud**   | Use Google GKE, AWS EKS, or Azure AKS for production. |
| **Self-hosted** | Deploy and manage your own Kubernetes cluster on-premises or on cloud VMs. |

### Example (Minikube)

# Install Minikube

```python
brew install minikube kubectl   # macOS


choco install minikube kubectl  # Windows

# Start a cluster
minikube start

```

---

## 5. What Happens When You Deploy YAML

##### When you apply a YAML file with:

```python
kubectl apply -f deployment.yaml

```

##### Here’s the behind-the-scenes workflow:

- kubectl talks to the API Server (control plane).

- The API server stores your configuration (desired state).

- The Controller Manager compares desired vs. actual state.

- If differences exist, the Scheduler assigns pods to nodes.

- The Kubelet on each node pulls the image and starts the container.

##### Kubernetes constantly ensures that the actual state matches the desired state you declared in your YAML.


## 6. Key YAML Object Types You Will See

# Kubernetes Objects

| Object | Purpose |
|--------|---------|
| **Pod** | Smallest deployable unit — runs one or more containers. |
| **Deployment** | Manages identical pods, keeps them running. |
| **Service** | Exposes pods to internal/external traffic. |
| **ConfigMap** | Stores configuration data. |
| **Secret** | Stores sensitive information (tokens, passwords). |
| **Ingress** | Routes external HTTP/S traffic to your services. |
| **Job / CronJob** | Run one-time or scheduled tasks. |

