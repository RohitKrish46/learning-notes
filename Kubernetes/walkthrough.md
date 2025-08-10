# Kubernetes Notes (Beginner-Friendly)

## What is Kubernetes?
Kubernetes (often called **K8s**) is an **open-source container orchestration tool** developed by Google.  
It helps you **manage containerized applications** across different environments — such as development, testing, and production.

Think of Kubernetes as the **traffic controller + repair crew** for your containers. It decides:
- Where containers should run.
- How many should run.
- How to keep them healthy.

---

## Why Do We Need Container Orchestration Tools?
- Modern applications often use **microservices** (small, independent services).
- Each microservice is often packaged as a **container**.
- A real application might have **hundreds or thousands of containers** running.
- Managing them manually would be chaotic — orchestration tools like Kubernetes automate this.

---

## Key Features of Orchestration Tools
1. **High Availability (HA)**  
   Ensures the application runs with minimal downtime.  
   *Example:* If one node crashes, Kubernetes restarts the containers on another node.

2. **Scalability**  
   Adjusts the number of running containers based on demand.  
   *Example:* During a Black Friday sale, Kubernetes automatically adds more containers to handle traffic.

3. **Disaster Recovery**  
   Supports backup and restore of application data and state.  
   *Example:* You can restore the system after a data center outage.

---

## Kubernetes Architecture Overview
Kubernetes is built on a **Master–Worker** architecture.

```

Master Node
|
Worker Nodes → Containers

```

---

### Master Node
The **brain** of the cluster — controls everything.  

Key components:
- **API Server** → Main entry point to Kubernetes. Users send commands via:
  - `kubectl` (CLI)
  - UI dashboards
  - APIs

- **Controller Manager** → Monitors the cluster and ensures it moves toward the *desired state*.  
  *Example:* If a pod crashes, it creates a new one.

- **Scheduler** → Decides where to run new containers based on available resources.

- **etcd** → Key-value store for cluster data (configurations, current state, etc.).  
  - Essential for backup and restore.

💡 In production, run **multiple master nodes** for high availability.

---

### Worker Nodes
- Run the actual application containers inside **Pods**.
- Each node runs a **kubelet** — an agent that talks to the master node and runs containers as instructed.
- Worker nodes typically have **more resources** than master nodes.

---

### Networking
- Kubernetes creates a **virtual network** across all nodes so they act like a single machine.
- This enables pods on different nodes to talk to each other.

---

## Kubernetes Core Components

### Pod
- Smallest deployable unit in Kubernetes.
- Wraps one or more containers.
- Example: A pod might contain:
  - A web server container.
  - A sidecar container for logging.

---

### Node
- A machine (VM or physical server) that runs pods.

---

### Service
- A **stable IP** to access pods.
- Works as a **load balancer** for multiple pods.  
  *Example:* If you have 3 replicas of your app, a Service sends traffic to all of them.

---

### Ingress
- Lets **external traffic** into your cluster.  
  *Example:* Making your app accessible on `myapp.com`.

---

### ConfigMap
- Stores **non-sensitive config data** (e.g., API endpoint URLs).

---

### Secret
- Stores **sensitive data** (e.g., passwords, tokens) in base64 encoded form.

---

### Volume
- Persistent storage for pods.
- Can be:
  - Local (on the same node).
  - External (cloud storage, network drive).
- Ensures data survives pod restarts.  
  *Example:* Database data stays even if the pod restarts.

💡 Kubernetes **does not** provide persistence by default — you must configure volumes.

---

### Deployment
- Blueprint for creating and updating **stateless** applications.
- Handles replication and rolling updates.  
  *Example:* Deploying 5 identical web server pods.

---

### StatefulSet
- Like Deployment but for **stateful** apps (databases, message queues).
- Maintains **stable network IDs** and storage.  
  *Example:* Running a MySQL cluster inside Kubernetes.

💡 Often, databases are hosted **outside** Kubernetes for simplicity.

---

## Kubernetes Configuration
- All configurations are given to Kubernetes in **YAML or JSON** (declarative format).
- The API server receives your request and compares:
  - **Current State** (from etcd)
  - **Desired State** (from your YAML)
- The Controller Manager makes changes until both match.

---

### Example Deployment YAML
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
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

This creates **3 pods** running Nginx and ensures they stay running.

---

## Summary Table

| Component   | Purpose                                     |
| ----------- | ------------------------------------------- |
| Pod         | Smallest deployable unit (wraps containers) |
| Node        | Server that runs pods                       |
| Service     | Stable IP & load balancer for pods          |
| Ingress     | Allows external traffic                     |
| ConfigMap   | Stores non-sensitive config                 |
| Secret      | Stores sensitive config                     |
| Volume      | Persistent storage for pods                 |
| Deployment  | Manages stateless app pods                  |
| StatefulSet | Manages stateful app pods                   |
