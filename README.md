

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

> You have multiple containers (Docker images) that you want to run reliably across several machines (called nodes). Kubernetes is the *manager* that ensures they stay healthy, available, and scalable.

### Kubernetes handles:
- Container **scheduling** and **placement**
- Starting your containers (called pods in Kubernetes)
- **Self-healing** (restarts crashed containers)
- **Scaling** (up/down automatically)
- **Load balancing** (distributing the containers across several servers)
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

Kubernetes uses **YAML files** as blueprints to describe what you want it to create or manage. YAML files essentially tells Kubernetes what you want it to do.

These are **declarative** — meaning you describe the *desired state*, and Kubernetes figures out *how* to do or reach it.

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

#### When you run:

```python
kubectl apply -f my-webapp.yaml
```

You are essentailly saying:

“Hey Kubernetes, I want three containers running nginx, listening on port 80.”

Then Kubernetes:

- Schedules 3 pods on the available worker nodes.

- Monitors them to make sure they’re running.

- Restarts them if they crash.

- Balances them across the cluster.

So yes — it’s the YAML file that defines what pods, services, and deployments should exist, but Kubernetes itself is the system that brings them to life.
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

---

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

---

## 7. Kubernetes in DevOps

##### Kubernetes is a core building block in DevOps pipelines.

### 🔹 Example Workflow:

- Developer pushes code → triggers CI pipeline.

- CI pipeline builds a Docker image and pushes it to a registry.

- CD pipeline updates a Kubernetes Deployment via YAML.

- Kubernetes rolls out new pods zero-downtime.

### 🔹 DevOps YAML Example — CI/CD Deployment

```python
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: mycompany/backend:1.0.5
        ports:
        - containerPort: 8080
```

##### This YAML would be automatically updated by Jenkins, ArgoCD, or GitHub Actions whenever a new image tag (1.0.6, 1.0.7, etc.) is built.

##### Kubernetes handles rolling updates gracefully, ensuring no downtime.

---

## 8. Kubernetes in MLOps

#### Kubernetes enables scalable, reproducible ML workflows by orchestrating:

- Training jobs

- Model serving

- Data preprocessing

- Batch inference

- Monitoring


### 🔹 Example: ML Model Training Job

```python
apiVersion: batch/v1
kind: Job
metadata:
  name: train-iris-model
spec:
  template:
    spec:
      containers:
      - name: trainer
        image: myregistry/ml-train:latest
        command: ["python", "train.py"]
        resources:
          limits:
            cpu: "4"
            memory: "8Gi"
      restartPolicy: Never
```

#### This YAML:

- Runs a one-time ML training task (train.py).

- Uses Kubernetes’ job management to ensure completion.

- Can be scaled horizontally for distributed training (e.g., TensorFlow, PyTorch).

### 🔹 Example: Model Serving Deployment (using FastAPI)

```python
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iris-model-server
spec:
  replicas: 2
  selector:
    matchLabels:
      app: iris-model
  template:
    metadata:
      labels:
        app: iris-model
    spec:
      containers:
      - name: model-server
        image: myregistry/iris-serving:latest
        ports:
        - containerPort: 8000
```

#### This deployment:

- Serves a trained ML model (FastAPI or Flask REST endpoint).

- Can be autoscaled based on CPU/traffic.

- Fits seamlessly into an MLOps pipeline using Kubeflow, ZenML, or MLflow.

---

## 9. Hands-On Example — Deploy NGINX Locally

#### Now let us walk through a complete example locally using Minikube.

### Step 1: Start Minikube

```python
minikube start
```

### Step 2: Create Deployment YAML

##### Create nginx-deployment.yaml:

```python
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

### Step 3: Create Service YAML

##### Create nginx-service.yaml:

```python
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30001
```

### Step 4: Deploy

```python

kubectl apply -f nginx-deployment.yaml

kubectl apply -f nginx-service.yaml

```

### Step 5: Verify

```python

kubectl get pods

kubectl get svc

```

### Step 6: Access App

```python

minikube service nginx-service

```

Visit:

```python
http://127.0.0.1:30001

```

You should see the default NGINX welcome page.

### Step 7: Clean Up

```python

kubectl delete -f nginx-deployment.yaml

kubectl delete -f nginx-service.yaml

minikube stop
```

### Next Step (Optional)

##### Extend this demo with Auto-Scaling:

```python
kubectl autoscale deployment nginx-deployment --cpu-percent=70 --min=2 --max=5
```

# Final Mental Model

| Concept | Example |
|---------|---------|
| **Cluster** | Minikube, EKS, AKS |
| **Deployment** | Manages pods for your app |
| **Service** | Exposes pods to users |
| **YAML** | Blueprint of what you want |
| **kubectl apply** | Command to create/update resources |
| **Pods** | Running containers inside Kubernetes |
| **Kubelets** | Download Docker images and run containers |






## Conclusion

#### Kubernetes is at the core of modern enterprise infrastructure — powering both DevOps and MLOps by providing:

- Scalability and resilience

- Infrastructure as code (YAML)

- Automated deployments

- Multi-cloud portability

- Support for AI/ML pipelines

##### Whether you are deploying web backends, data pipelines, or AI models, Kubernetes ensures your workloads are automated, reliable, and production-ready.

