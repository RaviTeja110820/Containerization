# 🐳➡️☸️ Docker Swarm vs Kubernetes – Complete Detailed Notes

---

# 📌 What is Kubernetes?

**Kubernetes (K8s)** is an **open-source container orchestration platform** used to:

* Deploy applications
* Manage containers
* Scale automatically
* Maintain high availability
* Handle networking & storage

👉 In simple words:

```
Kubernetes ensures your application is always running 
in the desired state automatically.
```

---

# ⚔️ Docker Swarm vs Kubernetes (Detailed Comparison)
```
| Feature             | Docker Swarm            | Kubernetes                                |
| ------------------- | ----------------------- | ----------------------------------------- |
| Installation        | Built-in with Docker    | Needs separate installation               |
| Container Runtime   | Only Docker             | Any CRI (containerd, CRI-O, Docker)       |
| Setup Complexity    | Easy                    | Complex                                   |
| Architecture        | Manager + Worker        | Master (Control Plane) + Worker           |
| Scheduling          | Simple                  | Advanced                                  |
| Auto Scaling        | ❌ Not supported         | ✅ Supported (HPA)                         |
| Load Balancing      | Built-in                | Advanced (Services, Ingress)              |
| Networking          | Overlay network         | CNI (Calico, Flannel, etc.)               |
| Storage             | Basic volumes           | PV, PVC, external storage                 |
| Objects             | Single object (Service) | Multiple (Pod, Deployment, Service, etc.) |
| GUI                 | ❌ No GUI (community)    | ✅ Dashboard available                     |
| Deployment Strategy | Limited                 | Rolling, Canary, Blue-Green               |
| Fault Tolerance     | Good                    | Excellent                                 |
| Ecosystem           | Limited                 | Very large (ArgoCD, Helm, etc.)           |
```
---

# 🏗️ Kubernetes Architecture (Detailed)


![Architecture](images/architecture.jpg)




```
                        USER
                         │
                         ▼
                    kubectl CLI
                         │
                         ▼
                ┌──────────────────┐
                │   API SERVER     │  ← Entry point
                └────────┬─────────┘
                         │
        ┌───────────────┼────────────────┐
        ▼               ▼                ▼
   ┌──────────┐   ┌────────────┐   ┌──────────────┐
   │  ETCD    │   │ Scheduler  │   │ Controller   │
   │ (DB)     │   │            │   │ Manager      │
   └──────────┘   └────────────┘   └──────────────┘
                         │
                         ▼
        ┌────────────────────────────────────┐
        │         WORKER NODES               │
        │                                    │
        │  ┌────────────┐  ┌────────────┐    │
        │  │ kubelet    │  │ kubelet    │    │
        │  │ runtime    │  │ runtime    │    │
        │  │ pods       │  │ pods       │    │
        │  └────────────┘  └────────────┘    │
        └────────────────────────────────────┘
```

```text
send req
create 5 containers
        │
        ▼
   ┌──────────┐
   │ kubectl  │
   └──────────┘
        │
        │ receives the request
        ▼
   ┌────────────┐
   │ apiServer  │◄────────────────────────────┐
   └────────────┘                             │
        │                                     │
        │ containers are pending              │
        ▼                                     │
   ┌──────────────┐                           │
   │    ETCD      │                           │
   │ stores all   │                           │
   │ information  │                           │
   └──────────────┘                           │
        ▲                                     │
        │                                     │
        │ updates status                      │
        │                                     │
        ▼                                     │
   ┌──────────────┐                           │
   │  Scheduler   │───────────────────────────┘
   └──────────────┘
        │
        │ decide and inform apiServer
        │ containers create on worker1
        ▼
   ┌────────────┐
   │ apiServer  │
   └────────────┘
        │
        │ create 5 containers
        ▼
   ┌────────────────────┐
   │ Worker1            │
   │ kubelet            │
   └────────────────────┘
        │
        │ inform apiServer that
        │ 5 containers are created
        ▼
   ┌──────────┐
   │ Docker   │
   └──────────┘
        │
        │ pull the image
        ▼
   ┌────────────┐
   │ DockerHub  │
   └────────────┘
        │
        │ pull the image
        ▼
   ┌──────────────────────────────┐
   │        RUN THE IMAGE         │
   │   ⬡   ⬡   ⬡   ⬡   ⬡         │
   │   (5 containers running)     │
   └──────────────────────────────┘
```


---

# 🧠 Component Explanation

## 1. API Server

* Entry point of Kubernetes
* All commands go through this

---

## 2. ETCD

* Key-value database
* Stores:

  * Nodes
  * Pods
  * Cluster state

📍 Location:

```
/var/lib/etcd
```

⚠️ Always take backup

---

## 3. Scheduler

* Decides **which node runs the pod**

---

## 4. Controller Manager

* Maintains desired state

Example:

```
Desired = 3 pods
Running = 2
→ Creates 1 more pod
```

---

## 5. Kubelet (Worker Agent)

* Runs on worker nodes
* Executes instructions

---

## 6. Container Runtime

* Pulls images
* Runs containers

---

# 🔁 Kubernetes Complete Request Flow

```
User (kubectl apply)
        │
        ▼
API Server
        │
        ▼
ETCD (store desired state)
        │
        ▼
Scheduler (choose node)
        │
        ▼
Kubelet (worker node)
        │
        ▼
Container Runtime
        │
        ▼
Pull Image (DockerHub)
        │
        ▼
Pod Created
```

---

# 🔄 Replica Creation Flow

```
kubectl create deployment nginx --replicas=3
        │
        ▼
API Server
        │
        ▼
ETCD stores replicas = 3
        │
        ▼
Scheduler assigns nodes
        │
        ▼
Workers create pods
        │
        ▼
3 Pods Running
```

---

# 💥 Failure Handling (Self-Healing)

```
Pod crashes
    │
    ▼
Kubelet detects failure
    │
    ▼
Controller Manager
    │
    ▼
New Pod Created
```

---

# 🌐 Kubernetes Networking (CNI)

Kubernetes uses **CNI (Container Network Interface)**:
```
| Plugin  | Type       | Features                 |
| ------- | ---------- | ------------------------ |
| Calico  | VXLAN/IPIP | Network policies, secure |
| Flannel | VXLAN      | Simple                   |
| Weave   | Mesh       | Encrypted                |
| Canal   | Hybrid     | Calico + Flannel         |
| Cilium  | eBPF       | Advanced                 |
```
---

# 📡 Networking Flow

```
Pod A (Worker1)
      │
      ▼
CNI (VXLAN tunnel)
      │
      ▼
Pod B (Worker2)
```

---

# 🧩 Kubernetes Cluster Setup Methods

## 1. Hard Way ❌

* Manual install
* Not used now

---

## 2. kubeadm ✅

* Install:

  * containerd
  * kubeadm
  * kubelet
  * kubectl

---

## 3. Managed (Cloud) ☁️

| Cloud | Service |
| ----- | ------- |
| AWS   | EKS     |
| GCP   | GKE     |
| Azure | AKS     |

---

# ⚙️ Minimum Requirements

```
OS: Ubuntu 22
RAM: 4GB
CPU: 2 Core
```

---

# 🛠️ Kubernetes Setup (Step-by-Step)

## Install Containerd

```bash
# install container runtime
wget https://raw.githubusercontent.com/lerndevops/labs/master/scripts/installContainerd.sh -P /tmp
bash /tmp/installContainerd.sh
systemctl restart containerd
```

---

## Install Kubernetes Components

```bash
# install kubeadm, kubelet, kubectl
wget https://raw.githubusercontent.com/lerndevops/labs/master/scripts/installK8S.sh -P /tmp
bash /tmp/installK8S.sh
```

---

## Initialize Master

```bash
kubeadm init --ignore-preflight-errors=all
```

---

## Configure kubectl

```bash
mkdir -p $HOME/.kube
cp /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
```

---

## Install Network (Calico)

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.24.1/manifests/calico.yaml
```

---

## Join Worker Node

```bash
# generate token on master
kubeadm token create --print-join-command
```

Run on worker:

```bash
kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash <hash>
```

---

# 🧪 Important kubectl Commands

```bash
# cluster info
kubectl cluster-info

# nodes
kubectl get nodes

# pods
kubectl get pods
kubectl describe pod <name>
kubectl logs <pod>

# deployment
kubectl create deployment myapp --image=nginx
kubectl scale deployment myapp --replicas=3

# services
kubectl expose deployment myapp --type=NodePort --port=80
kubectl get svc

# apply yaml
kubectl apply -f app.yaml
kubectl delete -f app.yaml
```

---

# 🔄 End-to-End Real Flow (Interview Ready)

```
kubectl → API Server → ETCD → Scheduler → Kubelet → Runtime → Pod
```

---

# 🧠 Key Concepts from Images (Important Points Covered)
```
✔ Manager vs Worker (Swarm vs K8s difference)
✔ API Server flow and scheduling
✔ ETCD stores cluster metadata
✔ Kubelet pulls images and creates pods
✔ CNI provides networking (VXLAN tunnels)
✔ Replicas handled by controller
✔ Pods created across worker nodes
✔ Cluster state always maintained
```
---

# 🧾 One-Line Summary

```
Kubernetes is an advanced orchestration system that automates deployment, scaling, networking, and self-healing of containerized applications.
```

---

# 🚀 Final Tip (Important)

If you remember only this:

```
User → API Server → ETCD → Scheduler → Worker → Pod
```

👉 You can answer **80% of Kubernetes questions**

---

# ☸️ Kubernetes POD & Cluster Setup Notes (GCP + Core Concepts)

---

# 📌 Creating Kubernetes Cluster on GCP (GKE)

## What was done

* Created a **ready-made Kubernetes cluster** using **GKE (Google Kubernetes Engine)**

---

## 💰 Free Tier Best Practices

```text
✔ Use ZONE-based cluster (cheaper)
✔ Use only 1 worker node
✔ Free for 90 days (trial)
```

---

## 🌐 Steps (High Level)

1. Go to GCP Console
2. Navigate to **Kubernetes Engine → Clusters**
3. Click **Create Cluster**
4. Choose:

   * Zone-based cluster
   * 1 node
5. Create cluster
6. Connect using:

```bash
# connect via cloud shell
gcloud container clusters get-credentials <cluster-name> --zone <zone>
```

---
---------------------------------------------------------------------------
# 📦 Kubernetes POD (Core Concept)

## 📊 Diagram Representation

```text
┌────────────────────────────┐
│           POD              │
│                            │
│      ⬡ container           │
│                            │
└────────────────────────────┘
```

---

## 📌 What is a POD?

A **Pod** is:

```text
Smallest deployable unit in Kubernetes
```

---

## 🧠 Key Points

* Pod contains **one or more containers**
* Kubernetes **does NOT manage containers directly**
* It manages **Pods**

---

## 🔄 Main Purpose (Very Important)

```text
Self-Healing
```

* If container fails → Pod restarts it
* Ensures application is always running

---

## 🛡️ Why Pod?

* Acts as **protective layer** around container
* Handles:

  * Restart
  * Health check
  * Lifecycle

---

## 🧾 Easy Understanding

```text
POD ≈ Container (in Kubernetes world)
```

---

# 🧩 Multi-Container Pods

## 📌 When to Use?

Use when containers must **work together tightly**

---

## 🔥 Real Use Cases

* Istio sidecar containers
* Prometheus exporters
* Logging agents
* Monitoring tools

---

## 📊 Example

```text
POD
 ├── nginx (main app)
 ├── tomcat (support)
 └── ubuntu (helper container)
```

---

# 📄 Kubernetes YAML Structure

Every Kubernetes YAML has **4 main sections**:

```yaml
apiVersion:   # version of API
kind:         # object type (Pod, Deployment, etc.)
metadata:     # name, labels
spec:         # actual configuration
```

---

# 🔍 YAML Fields Explanation

## apiVersion

* Defines API version
* Example: `v1`

---

## kind

* Type of object
* Example:

```yaml
kind: Pod
```

---

## metadata

* Contains:

  * name
  * labels

---

## spec

* Defines:

  * containers
  * images
  * commands

---

# 🧪 Multi-Container Pod Example

```yaml
# create a pod with multiple containers
apiVersion: v1
kind: Pod

metadata:
  name: pod2
  labels:
    author: sonal
    type: webserver
    env: dev

spec:
  containers:
    - name: c1
      image: nginx

    - name: c2
      image: tomcat

    - name: c3
      image: ubuntu
      # keep container running
      command: ["bash", "-c", "sleep 6000"]
```

---

# ⚙️ Commands (Step-by-Step)

## Create Pod

```bash
# create pod from yaml
kubectl create -f pod-definition.yml
```

---

## Check Pods

```bash
# list pods
kubectl get pods
```

---

## View Logs

```bash
# logs of specific container
kubectl logs pod2 -c c1
```

---

## Get Containers in Pod

```bash
# list container names inside pod
kubectl get pods pod2 -o jsonpath='{.spec.containers[*].name}'
```

---

## Delete Pod

```bash
# delete pod
kubectl delete pod pod2
```

---

# 🛠️ Troubleshooting Commands

```bash
# check status
kubectl get pods

# detailed info
kubectl describe pod <pod-name>

# logs
kubectl logs <pod-name> -c <container>
```

---

# 🔁 Pod Lifecycle (Flow)

```text
kubectl apply
      │
      ▼
API Server
      │
      ▼
Scheduler
      │
      ▼
Worker Node (kubelet)
      │
      ▼
Pod Created
      │
      ▼
Container Running
      │
      ▼
If fails → Restart (Self-Healing)
```

---

# 🧠 Key Takeaways

✔ Pod is smallest unit
✔ Pod manages container lifecycle
✔ Supports multi-container architecture
✔ Provides self-healing
✔ Always work with Pods (not containers)

---

# 🧾 One-Line Summary

```text
A Pod is the smallest deployable unit in Kubernetes that manages and protects one or more containers.
```

---

# ☸️ Kubernetes Objects – POD (Detailed Notes)

---

## 📌 Official Reference

```text
https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.31/#-strong-api-groups-strong-
```

---

# 📦 What is a POD?

A **Pod** is the **smallest deployable unit in Kubernetes**.

```text
Pod = wrapper around one or more containers
```

---

## 🧠 Key Points

* Kubernetes works with **Pods (not containers directly)**
* Pod ensures:

  * container is running
  * restart on failure (self-healing)
* Can contain:

  * single container
  * multiple containers (sidecar pattern)

---

# 🧱 POD YAML Structure

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: nginx
      image: nginx
```

---

# 🔍 FIELDS (Detailed Explanation)

---

## 1️⃣ apiVersion `<string>`

### 📌 Definition

```text
APIVersion defines the versioned schema of this representation of an object.
```

---

### 🧠 Explanation

* Specifies **which Kubernetes API version** is used
* Kubernetes converts it internally to latest supported version
* If version is invalid → request is rejected ❌

---

### 🔹 Example

```yaml
apiVersion: v1
```

---

### 🔗 More Info

```text
https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources
```

---

## 2️⃣ kind `<string>`

### 📌 Definition

```text
Kind is a string value representing the REST resource this object represents.
```

---

### 🧠 Explanation

* Defines **type of Kubernetes object**
* Must be written in **CamelCase**
* Cannot be updated after creation

---

### 🔹 Example

```yaml
kind: Pod
```

---

### 🔗 More Info

```text
https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds
```

---

## 3️⃣ metadata `<ObjectMeta>`

### 📌 Definition

```text
Standard object's metadata (information about the object)
```

---

### 🧠 Explanation

Stores identifying and organizing details like:

* name
* labels
* annotations

---

### 🔹 Example

```yaml
metadata:
  name: mypod
  labels:
    app: web
    env: dev
```

---

### 🧠 Important Note

```text
If labels of ReplicaSet are empty → they default to Pod labels
```

---

### 🔗 More Info

```text
https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata
```

---

## 4️⃣ spec `<PodSpec>`

### 📌 Definition

```text
Spec defines the desired behavior of the Pod
```

---

### ⚠️ Correction (Important)

```text
Your note says: ReplicaSetSpec ❌
Correct is: PodSpec ✅
```

---

### 🧠 Explanation

This is the **most important section**

Defines:

* containers
* images
* commands
* volumes
* networking

---

### 🔹 Example

```yaml
spec:
  containers:
    - name: nginx
      image: nginx
```

---

### 🔗 More Info

```text
https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status
```

---

# 🔄 How These Fields Work Together

```text
apiVersion → defines API version
kind       → defines object type
metadata   → identifies object
spec       → defines how it should run
```

---

# 🧠 Easy Memory Trick

```text
apiVersion → WHAT version
kind       → WHAT object
metadata   → WHO (name/info)
spec       → HOW it should run
```

---

# 🧾 One-Line Summary

```text
A Pod YAML defines API version, object type, metadata, and desired state to run containers in Kubernetes.
```
# Kubernetes Init Containers

## Introduction

In Kubernetes, a Pod can contain:

* One or more Application Containers
* One or more Init Containers

Normally, when a Pod starts, Kubernetes launches the application containers immediately.

However, sometimes an application requires some setup work before it can start, such as:

* Waiting for a database
* Creating configuration files
* Downloading data
* Verifying network connectivity
* Running initialization scripts

For these tasks, Kubernetes provides:

```text
Init Containers
```

---

# What is an Init Container?

An Init Container is a special container that runs **before** the application containers start.

Its primary purpose is to perform initialization tasks required by the application.

---

## Simple Flow

```text
Pod Created
     |
     v

Init Container Starts
     |
     v

Initialization Completed
     |
     v

Application Container Starts
```

---

# Key Characteristics of Init Containers

## 1. Run Before Application Containers

Init containers always execute before application containers.

```text
Init Container
      |
      v
App Container
```

Application containers will not start until all init containers complete successfully.

---

## 2. Must Complete Successfully

Init containers must exit successfully.

```text
Exit Code = 0
```

Only then Kubernetes starts the next container.

---

## 3. Run Sequentially

If multiple init containers exist:

```text
init-1
   |
   v

init-2
   |
   v

init-3
   |
   v

Application Containers
```

Each init container must finish before the next begins.

---

## 4. Run Only Once

Init containers run only during Pod startup.

They do not run continuously like application containers.

---

## 5. Application Containers Wait

Application containers remain in:

```text
Waiting
```

state until all init containers succeed.

---

# Why Use Init Containers?

## Scenario 1: Wait for Database

Suppose a web application depends on MySQL.

Without Init Container:

```text
Web App Starts
      |
      v

Database Not Ready
      |
      v

Application Failure
```

With Init Container:

```text
Check Database
      |
      v

Database Ready?
      |
      v

Yes
      |
      v

Start Web App
```

---

## Scenario 2: Download Configuration Files

Before starting the application:

```text
Download Config
      |
      v

Store in Volume
      |
      v

Start Application
```

---

## Scenario 3: Run Setup Scripts

Example:

```text
Create Directories
Configure Files
Generate Certificates
```

before application startup.

---

# Advantages of Init Containers

## Separate Setup Logic

No need to include extra tools inside the application image.

Example:

Instead of modifying your application image:

```text
nginx + curl + sed + awk + python
```

Use:

```text
Init Container → curl, awk, sed
App Container → nginx
```

This keeps application images small and secure.

---

## Better Security

Unnecessary tools stay outside the application container.

```text
Init Container
    |
    +--> wget
    +--> curl
    +--> bash

App Container
    |
    +--> Only Application
```

Smaller attack surface.

---

## Dependency Verification

Applications start only when:

```text
Database Ready
Network Available
Config Present
```

---

# Init Container vs Regular Container

| Feature                    | Init Container | Application Container |
| -------------------------- | -------------- | --------------------- |
| Runs Before App            | Yes            | No                    |
| Runs Continuously          | No             | Yes                   |
| Must Complete Successfully | Yes            | No                    |
| Supports Probes            | No             | Yes                   |
| Runs Only Once             | Yes            | No                    |
| Used For Initialization    | Yes            | No                    |

---

# Limitations of Init Containers

Init Containers do NOT support:

```text
Liveness Probe
Readiness Probe
Startup Probe
Lifecycle Hooks
```

Reason:

```text
Init Containers must finish execution.
```

They are not long-running services.

---

# Init Container Lifecycle

```text
Pod Created
     |
     v

Init Container-1
     |
     v

Success
     |
     v

Init Container-2
     |
     v

Success
     |
     v

Application Container
     |
     v

Running
```

---

# What Happens if Init Container Fails?

Suppose:

```text
Init Container Failed
```

Kubernetes behavior:

```text
Init Container Restarted
      |
      v

Retry Until Success
```

---

## Example

```text
Database Unavailable
      |
      v

Init Container Fails
      |
      v

Restart
      |
      v

Retry
      |
      v

Database Available
      |
      v

Success
      |
      v

Application Starts
```

---

# Restart Policy Impact

## restartPolicy: Always

```text
Init Container Fails
      |
      v

Retry Until Success
```

---

## restartPolicy: Never

```text
Init Container Fails
      |
      v

Pod Marked Failed
```

No retries.

---

# Demo 1 - Waiting for Database

## YAML File

```bash
# Create YAML file
vim initcontdemo1.yml
```

---

## Original YAML

```yaml
kind: Pod

apiVersion: v1

metadata:

  # Pod name
  name: init-cont-pod1

  namespace: default

  labels:

    # Custom label
    role: dev

spec:

  # Restart failed containers
  restartPolicy: Always

  ##################################################
  # Init Container
  ##################################################

  initContainers:

  - name: initcont1

    image: ubuntu

    # Try connecting to database
    command:
    - bash
    - -c
    - telnet mydb 1521

  ##################################################
  # Application Container
  ##################################################

  containers:

  - name: tcont

    image: httpd
```

---

# Problem with Demo 1

The command:

```bash
telnet mydb 1521
```

requires:

```text
mydb DNS
Database Service
Port 1521
```

If database does not exist:

```text
Init Container Fails
```

---

## Result

```text
Pod Status

Init:CrashLoopBackOff
```

because Kubernetes keeps retrying.

---

# Corrected Version

Example:

```yaml
command:
- bash
- -c
- sleep 5
```

This always succeeds.

---

# Demo 2 - Successful Init Container

## YAML File

```bash
# Create file
vim initcontdemo2.yml
```

---

## Complete YAML

```yaml
apiVersion: v1

# Pod object
kind: Pod

metadata:

  # Pod name
  name: init-cont-pod2

  namespace: default

  labels:

    # Pod label
    role: dev

spec:

  # Restart failed containers
  restartPolicy: Always

  ##################################################
  # Init Container
  ##################################################

  initContainers:

  - name: initcont1

    # Ubuntu image
    image: ubuntu

    # Wait for 5 seconds
    command:
    - bash
    - -c
    - sleep 5

  ##################################################
  # Main Application Container
  ##################################################

  containers:

  - name: tcont

    # Apache HTTPD image
    image: httpd
```

---

# Deploy the Pod

```bash
# Create Pod
kubectl apply -f initcontdemo2.yml
```

---

# Monitor Pod Status

```bash
# Watch pod lifecycle
watch kubectl get pods
```

---

## What You Will Observe

Initially:

```text
NAME             READY   STATUS
init-cont-pod2   0/1     Init:0/1
```

---

After Init Container Completes:

```text
NAME             READY   STATUS
init-cont-pod2   1/1     Running
```

---

# Detailed Startup Flow

```text
Pod Created
     |
     v

Init Container Started
     |
     v

sleep 5
     |
     v

Init Container Completed
     |
     v

Apache Container Started
     |
     v

Pod Running
```

---

# Viewing Init Container Logs

To view logs of Init Container:

```bash
# View init container logs
kubectl logs init-cont-pod2 -c initcont1
```

---

# Viewing Pod Details

```bash
# Describe pod
kubectl describe pod init-cont-pod2
```

You will see:

```text
Init Containers:
    initcont1

Containers:
    tcont
```

---

# Real-World Example

## Web Application + Database

```text
Pod
 |
 +--------------------+
 | Init Container     |
 | Check MySQL        |
 +--------------------+
           |
           v
 +--------------------+
 | Application        |
 | Spring Boot        |
 +--------------------+
```

Flow:

```text
Check MySQL
      |
      v

MySQL Ready?
     / \
   No   Yes
   |      |
Retry     Start Application
```

---

# Multiple Init Containers Example

```yaml
initContainers:

# Create directory
- name: init-1
  image: busybox
  command: ["sh","-c","mkdir /data"]

# Download file
- name: init-2
  image: busybox
  command: ["sh","-c","wget example.com"]

# Validate configuration
- name: init-3
  image: busybox
  command: ["sh","-c","echo Validation Complete"]
```

Execution order:

```text
init-1
   |
   v

init-2
   |
   v

init-3
   |
   v

Application Container
```

---

# Interview Questions

## Q1. What is an Init Container?

An Init Container is a special container that runs before application containers start.

---

## Q2. Can Init Containers run continuously?

No.

They run once and exit.

---

## Q3. Can Init Containers use Liveness Probes?

No.

Init Containers do not support:

```text
Liveness Probe
Readiness Probe
Startup Probe
```

---

## Q4. What happens if an Init Container fails?

Kubernetes repeatedly restarts it until it succeeds.

---

## Q5. Why use Init Containers?

To:

* Wait for dependencies
* Download files
* Run initialization scripts
* Configure applications

---

## Q6. Do Application Containers start before Init Containers?

No.

Application containers start only after all init containers succeed.

---

# Summary

## Init Container Purpose

```text
Prepare Environment
Before Application Starts
```

---

## Key Features

```text
Runs Before App Container
Runs Once
Must Succeed
Sequential Execution
Supports Setup Tasks
```

---

## Common Use Cases

```text
Wait For Database
Download Files
Generate Configurations
Run Setup Scripts
Validate Dependencies
```

---

# Final Architecture Diagram

```text
                Pod Created
                     |
                     v

           +------------------+
           | Init Container 1 |
           +------------------+
                     |
                     v

           +------------------+
           | Init Container 2 |
           +------------------+
                     |
                     v

           +------------------+
           | Init Container 3 |
           +------------------+
                     |
                     v

           +------------------+
           | App Container(s) |
           +------------------+
                     |
                     v

                Pod Running
```

# Kubernetes Static Pods

## Introduction

In Kubernetes, Pods are usually created through the Kubernetes API Server.

For example:

```bash
kubectl create deployment nginx --image=nginx
```

When you execute this command:

1. Request goes to API Server.
2. API Server stores information in etcd.
3. Scheduler decides which node should run the Pod.
4. Kubelet on that node creates and runs the Pod.

---

## Who Actually Creates Pods?

Many people think the API Server creates Pods.

This is not correct.

The actual component responsible for creating and running Pods is:

```text
Kubelet
```

### Normal Pod Creation Flow

```text
kubectl
    |
    v
API Server
    |
    v
etcd
    |
    v
Scheduler
    |
    v
Kubelet
    |
    v
Pod Created
```

The API Server only receives the request.

The Kubelet actually creates and manages the Pod on the assigned node.

---

# What is a Static Pod?

A Static Pod is a Pod that is managed directly by the Kubelet.

Instead of creating a Pod through:

```bash
kubectl apply -f pod.yaml
```

we place the Pod YAML file directly in a directory monitored by the Kubelet.

The Kubelet reads the YAML file and creates the Pod automatically.

---

## Definition

```text
Static Pod = Pod created directly by Kubelet
```

No API Server interaction is required for creation.

---

# Characteristics of Static Pods

## Created Directly by Kubelet

Normal Pod:

```text
API Server → Kubelet → Pod
```

Static Pod:

```text
YAML File → Kubelet → Pod
```

---

## Stored on Node Filesystem

The Pod manifest file exists on the node itself.

Example:

```text
Worker Node
     |
     +---- static-pod.yml
```

---

## Automatically Monitored

Kubelet continuously watches the manifest directory.

Whenever a YAML file appears:

```text
YAML File Added
       |
       v
Kubelet Detects
       |
       v
Pod Created
```

---

## Automatically Recreated

If someone deletes the Pod:

```text
Pod Deleted
      |
      v
Kubelet Notices
      |
      v
Pod Recreated
```

because the YAML file still exists.

---

## Node-Specific

Static Pods always run on the node where the manifest file exists.

Example:

```text
Worker-1
   |
   +--> static-pod.yml
   |
   +--> static-pod Created

Worker-2
   |
   +--> No Pod
```

---

# Static Pod Architecture

```text
            Worker Node
                  |
                  |
                  v

     /etc/kubernetes/manifests
                  |
                  |
                  v

          static-pod.yml
                  |
                  |
                  v

              Kubelet
                  |
                  |
                  v

             Static Pod
```

---

# Static Pod Manifest Directory

Kubelet monitors a directory known as:

```text
Static Pod Path
```

For kubeadm clusters:

```bash
/etc/kubernetes/manifests
```

---

## Verify Kubelet Status

Login to worker node:

```bash
# Check kubelet status
service kubelet status
```

Expected:

```text
active (running)
```

---

# Navigate to Manifest Directory

```bash
# Go to manifest directory
cd /etc/kubernetes/manifests
```

Verify contents:

```bash
# List files
ls -l
```

You may see:

```text
etcd.yaml
kube-apiserver.yaml
kube-scheduler.yaml
kube-controller-manager.yaml
```

These are Kubernetes control-plane Static Pods.

---

# Creating a Static Pod

## Create YAML File

```bash
# Create static pod manifest
vim static-pod.yml
```

---

## Static Pod YAML

```yaml
apiVersion: v1

# Kubernetes Pod object
kind: Pod

metadata:

  # Labels for pod identification
  labels:

    run: static-pod

  # Pod name
  name: static-pod

spec:

  containers:

  - name: static-pod

    # Nginx container image
    image: nginx
```

---

## Save the File

Once saved inside:

```bash
/etc/kubernetes/manifests
```

Kubelet immediately detects it.

---

# What Happens Internally?

```text
static-pod.yml Created
         |
         v

Kubelet Detects YAML
         |
         v

Creates Pod
         |
         v

Pod Running
```

No API Server request is needed.

---

# Verify Static Pod

Go to Master Node:

```bash
# View pods
kubectl get pods
```

Example:

```text
static-pod-worker1
```

Notice:

```text
Node Name Appended
```

Kubelet automatically appends the node name.

---

# Verify Node Placement

```bash
# View pod details
kubectl get pods -o wide
```

Example:

```text
NAME                  NODE
static-pod-worker1    worker1
```

Static Pod always runs on the same node.

---

# Static Pod Naming

Suppose:

```yaml
metadata:
  name: static-pod
```

and node name is:

```text
worker1
```

Pod name becomes:

```text
static-pod-worker1
```

---

# Deleting Static Pod

Try deleting the Pod:

```bash
# Delete pod
kubectl delete pod static-pod-worker1
```

---

## What Happens?

Within a few seconds:

```text
Pod Deleted
      |
      v
Kubelet Sees YAML Exists
      |
      v
Pod Recreated
```

The Pod comes back automatically.

---

# Why Does It Reappear?

Because:

```text
YAML File Still Exists
```

in:

```bash
/etc/kubernetes/manifests
```

Kubelet continuously ensures the Pod exists.

---

# How to Permanently Delete a Static Pod?

Deleting the Pod is not enough.

You must delete the YAML file.

---

## Step 1

Login to node.

```bash
# Go to manifest directory
cd /etc/kubernetes/manifests
```

---

## Step 2

Delete YAML file.

```bash
# Remove static pod manifest
rm static-pod.yml
```

---

## Step 3

Verify.

```bash
kubectl get pods
```

Now the Pod disappears permanently.

---

# Static Pod Recovery Flow

```text
Static Pod Deleted
         |
         v

Manifest Exists?
      /      \
    Yes       No
     |         |
     v         v

Recreate    Remove Pod
```

---

# Checking Kubelet Logs

If the Pod is not starting:

```bash
# View kubelet logs
journalctl -u kubelet.service | less
```

Useful for troubleshooting:

* YAML errors
* Image pull failures
* Container startup issues

---

# Why Were Static Pods Created?

A common interview question:

```text
If API Server creates Pods,
how was API Server itself created?
```

---

## Chicken and Egg Problem

Kubernetes components are Pods:

```text
kube-apiserver
etcd
kube-scheduler
controller-manager
```

But these components must exist before Kubernetes is operational.

So:

```text
Who creates them?
```

Answer:

```text
Kubelet using Static Pods
```

---

# Kubernetes Control Plane Components

Check:

```bash
kubectl get pods -n kube-system -o wide
```

You will see:

```text
kube-apiserver
etcd
kube-scheduler
kube-controller-manager
```

These are Static Pods.

---

# Control Plane Static Pod Architecture

```text
Master Node
     |
     v

/etc/kubernetes/manifests
     |
     +---------------------+
     | kube-apiserver.yaml |
     +---------------------+
     | etcd.yaml           |
     +---------------------+
     | scheduler.yaml      |
     +---------------------+
     | controller.yaml     |
     +---------------------+
            |
            v

         Kubelet
            |
            v

     Control Plane Pods
```

---

# kubeadm and Static Pods

When running:

```bash
kubeadm init
```

Kubernetes automatically:

1. Downloads control-plane manifests.
2. Places them inside:

```bash
/etc/kubernetes/manifests
```

3. Kubelet creates Static Pods.

---

# Verify Control Plane Manifests

On Master Node:

```bash
# Navigate to manifest directory
cd /etc/kubernetes/manifests
```

List files:

```bash
# View manifests
ls -l
```

Example:

```text
etcd.yaml
kube-apiserver.yaml
kube-scheduler.yaml
kube-controller-manager.yaml
```

---

# Static Pod vs Normal Pod

| Feature                    | Normal Pod | Static Pod |
| -------------------------- | ---------- | ---------- |
| Created Through API Server | Yes        | No         |
| Created By Kubelet         | Yes        | Yes        |
| YAML Stored in Cluster     | Yes        | No         |
| YAML Stored on Node        | No         | Yes        |
| Scheduled By Scheduler     | Yes        | No         |
| Bound to Specific Node     | No         | Yes        |
| Auto Recreated by Kubelet  | Yes        | Yes        |
| Used for Control Plane     | No         | Yes        |

---

# Static Pod Lifecycle

```text
Create YAML
      |
      v

Place in
/etc/kubernetes/manifests
      |
      v

Kubelet Detects
      |
      v

Pod Created
      |
      v

Pod Deleted?
      |
      v

Kubelet Recreates
      |
      v

Delete YAML
      |
      v

Pod Removed Permanently
```

---

# Real-World Usage

In production applications:

```text
Rarely Used
```

For application deployments:

Use:

```text
Deployment
StatefulSet
DaemonSet
Job
```

instead.

---

## Main Purpose of Static Pods

Used internally by Kubernetes for:

```text
kube-apiserver
etcd
kube-scheduler
kube-controller-manager
```

during cluster bootstrapping.

---

# Interview Questions

## Q1. What creates Pods in Kubernetes?

```text
Kubelet
```

---

## Q2. What is a Static Pod?

A Pod managed directly by Kubelet using a local manifest file.

---

## Q3. Where are Static Pod manifests stored?

```bash
/etc/kubernetes/manifests
```

---

## Q4. Can Static Pods be scheduled to another node?

```text
No
```

They always run on the node where the manifest file exists.

---

## Q5. Why does a Static Pod reappear after deletion?

Because the YAML file still exists in the manifest directory.

---

## Q6. How do you permanently delete a Static Pod?

Delete its YAML file from:

```bash
/etc/kubernetes/manifests
```

---

## Q7. Which Kubernetes components run as Static Pods?

```text
etcd
kube-apiserver
kube-scheduler
kube-controller-manager
```

---

# Summary

## Static Pod

```text
Created Directly By Kubelet
```

---

## Manifest Location

```bash
/etc/kubernetes/manifests
```

---

## Created Without

```text
API Server
Scheduler
```

---

## Main Use

```text
Control Plane Components
```

---

# Final Architecture Diagram

```text
                 kubeadm init
                       |
                       v

         /etc/kubernetes/manifests
                       |
      -----------------------------------
      |               |                |
      v               v                v

 kube-apiserver    etcd        kube-scheduler
      |
      |
      v

    Kubelet
      |
      v

 Creates Static Pods
      |
      v

 Kubernetes Cluster Starts
```


---
-----------------------------------------------------------------------------



# ☸️ Kubernetes Services (Service Object) – Detailed Notes

---

# 📌 What is a Service in Kubernetes?

A **Service** is a Kubernetes resource used to:

```text id="xq3j2r"
Expose applications running in Pods 
and enable communication between them
```

---

# 🧠 Simple Definition

```text id="9m0j3c"
Service = Stable way to access Pods
```

---

# 📦 Key Responsibilities of a Service

* Enables **Pod-to-Pod communication**
* Enables **external access to applications**
* Provides **stable IP & DNS**
* Performs **load balancing**

---

# ❓ Why Do We Need Services?

## 🚫 Problems Without Service

### 1. Pod IP is Not Stable

```text id="w9hh7h"
Pod gets recreated → IP changes
```

👉 Communication breaks ❌

---

### 2. No DNS Name for Pods

```text id="kk1h9h"
Pods cannot be accessed reliably by name
```

👉 Difficult to manage communication ❌

---

### 3. No External Access

```text id="h6zv1p"
Cannot access pod from outside cluster
```

👉 Application not reachable ❌

---

# ✅ Solution: Kubernetes Service

```text id="9fg0n1"
Service provides stable IP + DNS + Load balancing
```

---

# 🔄 How Service Works (Flow)

```text id="m9zztl"
Client / Pod
      │
      ▼
  Service (Stable IP)
      │
      ▼
   Pod 1
   Pod 2
   Pod 3
```

👉 Service distributes traffic to multiple Pods

---

# 🎯 Types of Kubernetes Services
```
| Type         | Use Case                     |
| ------------ | ---------------------------- |
| ClusterIP    | Internal communication       |
| NodePort     | External access via node IP  |
| LoadBalancer | External access via cloud LB |
```
---

# 1️⃣ ClusterIP (Default)

## 📌 Definition

```text id="xqk1cu"
Exposes service internally within the cluster
```

---

## 🧠 Key Points

* Default service type
* Accessible only inside cluster
* Provides:

  * Internal IP
  * DNS name

---

## 📊 Flow

```text id="3u6h0l"
Pod A → Service (ClusterIP) → Pod B
```

---

## 🔹 Example

```yaml id="r6e0qq"
apiVersion: v1
kind: Service
metadata:
  name: myservice

spec:
  type: ClusterIP
  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
```

---

# 2️⃣ NodePort

## 📌 Definition

```text id="y2h7r3"
Exposes service on a port of each node
```

---

## 🧠 Key Points

* Accessible from outside cluster
* Uses:

```text id="0r3y1h"
<NodeIP>:<NodePort>
```

* Port range:

```text id="qhl7xw"
30000 - 32767
```

---

## 📊 Flow

```text id="wz2l1v"
Browser → NodeIP:NodePort → Service → Pod
```

---

## 🔹 Example

```yaml id="a3b5ul"
apiVersion: v1
kind: Service

metadata:
  name: mynodeport

spec:
  type: NodePort

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
```

---

# 3️⃣ LoadBalancer

## 📌 Definition

```text id="e1p7rt"
Exposes service using cloud provider load balancer
```

---

## 🧠 Key Points

* Used in cloud (AWS, GCP, Azure)
* Automatically creates external load balancer
* Public IP provided

---

## 📊 Flow

```text id="dy1rm8"
User → LoadBalancer → Service → Pods
```

---

## 🔹 Example

```yaml id="9k5kzj"
apiVersion: v1
kind: Service

metadata:
  name: mylb

spec:
  type: LoadBalancer

  selector:
    app: nginx

  ports:
    - port: 80
      targetPort: 80
```

---

# 🔑 Important Concept: Selector

```yaml id="d1b9s2"
selector:
  app: nginx
```

👉 Service connects to pods using labels

---

# ⚖️ Comparison of Service Types
```
| Feature  | ClusterIP     | NodePort       | LoadBalancer    |
| -------- | ------------- | -------------- | --------------- |
| Scope    | Internal      | External       | External        |
| Access   | Pod → Pod     | Browser → Node | Public Internet |
| IP Type  | Cluster IP    | Node IP        | Public IP       |
| Use Case | Microservices | Testing        | Production      |
```
---

# 🔄 Real Example Scenario

```text id="9yxtxk"
Frontend Pod → Service → Backend Pod
```

👉 Backend can change, service remains same

---

# 🧠 Key Takeaways

✔ Service gives stable access
✔ Solves Pod IP problem
✔ Provides load balancing
✔ Enables internal + external communication

---

# 🧾 One-Line Summary

```text id="m2z0zo"
Service is a Kubernetes resource that provides stable networking and load balancing for Pods.
```

---

# ☸️ Kubernetes Service – ClusterIP (Detailed Notes with Demo)

---

![ClusterIP](images/cluster-ip.jpg)



# 📌 What is ClusterIP?

```text
ClusterIP is a Kubernetes Service type used for communication 
between Pods inside the cluster.
```

---

# 🧠 Simple Understanding

```text
Pod → Service (ClusterIP) → Pod
```

👉 Used only for **internal communication**

---

# 🎯 Use Case

```text
When one pod needs to talk to another pod inside the cluster
```

Example:

* Frontend Pod → Backend Pod
* App Pod → Database Pod

---

# ❗ Important Points

* Default service type
* Not accessible outside cluster
* Provides:

  * Stable IP
  * DNS name
* Uses **labels (selector)** to connect pods

---

# 🧪 DEMO – Pod to Pod Communication using ClusterIP

---

# Step 1️⃣ – Create Nginx Pod

```yaml
# nginx-pod.yml

apiVersion: v1              # API version
kind: Pod                  # creating a Pod

metadata:
  name: pod1               # pod name
  labels:
    app: webserver         # label used by service selector

spec:
  containers:
    - name: c1             # container name
      image: nginx         # nginx web server image
```

---

# Step 2️⃣ – Create Test Pod (Client Pod)

```yaml
# test-pod.yml

apiVersion: v1             # API version
kind: Pod                 # creating a Pod

metadata:
  name: test-pod          # client pod name

spec:
  containers:
    - name: c1            # container name
      image: ubuntu       # ubuntu image

      # keep container running
      command: ["bash", "-c", "sleep 6000"]
```

---

# Step 3️⃣ – Create ClusterIP Service

```yaml
# service.yml

apiVersion: v1
kind: Service

metadata:
  name: mysvc             # service name (used as DNS)

spec:
  type: ClusterIP         # service type

  selector:
    app: webserver        # connects to pods with this label

  ports:
    - port: 80            # service port
      targetPort: 80      # container port inside pod
```

---

# Step 4️⃣ – Apply YAML Files

```bash
# create nginx pod
kubectl create -f nginx-pod.yml

# create test pod
kubectl create -f test-pod.yml

# create service
kubectl create -f service.yml

# check services
kubectl get svc
```

---

# Step 5️⃣ – Verify Service Connection

```bash
# check endpoints (linked pods)
kubectl get endpoints
```

👉 This shows which pod is connected to service

---

# Step 6️⃣ – Access Service from Test Pod

```bash
# enter into test pod
kubectl exec -it test-pod -- bash
```

---

## Install curl inside pod

```bash
# update packages
apt-get update

# install curl
apt-get install curl -y
```

---

## Call Service using DNS name

```bash
# access nginx via service
curl mysvc
```

---

# 🔄 How It Works (Flow Diagram)

```text
test-pod (client)
      │
      ▼
   mysvc (ClusterIP Service)
      │
      ▼
   pod1 (nginx)
```

---

# 🧠 Key Concept (VERY IMPORTANT)

```text
Service does NOT connect to pod directly using IP
It uses LABEL SELECTOR
```

---

# ⚠️ Common Mistakes

* Labels mismatch ❌
* Wrong port mapping ❌
* Pod not running ❌

---

# 🧾 One-Line Summary

```text
ClusterIP allows internal communication between pods using a stable service name instead of dynamic pod IPs.
```

---

# ☸️ Kubernetes Services – NodePort & ExternalName (Detailed Notes)

---

# 🌐 NodePort Service

---

![NodePort Service](images/node-port.jpg)


# 📌 What is NodePort?

```text id="j8wq2m"
NodePort is a Service type that exposes a Pod to the outside world 
using a port on each node of the cluster.
```

---

# 🧠 Why NodePort?

## 🚫 Problem

```text id="b3s4xp"
❌ Cannot access Pod IP outside cluster  
❌ Cannot access ClusterIP outside cluster
```

---

## ✅ Solution

```text id="k9v8tn"
Expose the service externally using NodePort
```

---

# 🔑 Key Concepts

* Opens a port on **every node**
* Accessible using:

```text id="4d4o1q"
<NodeIP>:<NodePort>
```

---

## 🔢 NodePort Range

```text id="x7j3ys"
30000 - 32767
```

---

# 🔄 Flow Diagram

```text id="l1yazg"
Browser
   │
   ▼
NodeIP:NodePort
   │
   ▼
Service (NodePort)
   │
   ▼
Pod (nginx)
```

---

# 📄 NodePort YAML Example

```yaml id="t3s8z1"
apiVersion: v1
kind: Service

metadata:
  name: mysvc1              # service name

spec:
  type: NodePort            # expose service externally

  selector:
    app: webserver          # connects to pods with this label

  ports:
    - port: 80              # service port (internal)
      targetPort: 80        # container port inside pod

      # optional: if not given, Kubernetes assigns automatically
      # nodePort: 30007
```

---

# ⚙️ How to Access

```text id="c0z5m6"
http://<NodeIP>:<NodePort>
```

Example:

```text id="q3p4rl"
http://192.168.1.10:30007
```

## By default, GCP blocks NodePort traffic.

✅ Fix Firewall

Run this in Cloud Shell:
```bash
gcloud compute firewall-rules create allow-nodeport \
  --allow tcp:30000-32767 \
  --source-ranges=0.0.0.0/0 \
  --description="Allow NodePort access"
```
---

# ⚠️ Important Notes

* NodePort is mainly used for:

  * testing
  * basic external access
* Not recommended for production (use LoadBalancer / Ingress)

---

# 🌍 ExternalName Service

---

# 📌 What is ExternalName?

```text id="y0n7zx"
ExternalName is a Service that maps a Kubernetes service name 
to an external DNS name.
```

---

# 🧠 Simple Understanding

```text id="1y4kq9"
Internal name → External service
```

---

# 🔑 Key Features

* Does NOT create:

  * ClusterIP ❌
  * Proxy ❌
* Creates:

```text id="gq7y6b"
CNAME (DNS alias)
```

---

# 🎯 Use Cases

* Access external database
* Connect to external APIs
* Use legacy systems

---

# 🧪 DEMO – Step-by-Step

---

## Step 1️⃣ – Create Nginx Pod + Service

```yaml id="b1q0pe"
# nginx-pod-service.yml

apiVersion: v1
kind: Pod

metadata:
  name: nginx
  labels:
    app: nginx

spec:
  containers:
    - name: nginx
      image: nginx
---
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:
  selector:
    app: nginx            # connect to nginx pod

  ports:
    - protocol: TCP
      port: 80            # service port
      targetPort: 80      # container port
```

---

## 📍 Access Inside Cluster

```text id="y8q3c1"
nginx-service.default.svc.cluster.local:80
```

---

## Step 2️⃣ – Create ExternalName Service

```yaml id="d8v2op"
apiVersion: v1
kind: Service

metadata:
  name: nginx-external     # alias name

spec:
  type: ExternalName       # special service type

  externalName: nginx-service.default.svc.cluster.local
  # maps to internal service DNS
```

---

## 📍 DNS Mapping

```text id="q9z3yx"
nginx-external.default.svc.cluster.local 
        ↓
nginx-service.default.svc.cluster.local
```

---

## Step 3️⃣ – Create Ubuntu Pod (Client)

```yaml id="m2p8xq"
# ubuntu-pod.yaml

apiVersion: v1
kind: Pod

metadata:
  name: ubuntu

spec:
  containers:
    - name: ubuntu
      image: ubuntu

      # keep pod running
      command: ["sleep", "3600"]

  restartPolicy: Never
```

---

## Step 4️⃣ – Apply YAML Files

```bash id="r7z2lt"
# create resources
kubectl create -f nginx-pod-service.yml
kubectl create -f ubuntu-pod.yaml
kubectl create -f externalname.yml
```

---

## Step 5️⃣ – Access Service from Ubuntu Pod

```bash id="c4l1hv"
# enter ubuntu pod
kubectl exec -it ubuntu -- bash
```

---

## Install curl

```bash id="y2o5wd"
# update packages
apt update

# install curl
apt install -y curl
```

---

## Call ExternalName Service

```bash id="p7k4sm"
# access nginx via alias
curl http://nginx-external
```

---

# 🔄 Flow Diagram (ExternalName)

```text id="p9m6xn"
Ubuntu Pod
    │
    ▼
nginx-external (DNS alias)
    │
    ▼
nginx-service
    │
    ▼
nginx Pod
```

---

# ⚖️ NodePort vs ExternalName
```
| Feature       | NodePort        | ExternalName             |
| ------------- | --------------- | ------------------------ |
| Purpose       | External access | DNS alias                |
| Creates IP    | Yes             | No                       |
| Creates Proxy | Yes             | No                       |
| Use Case      | Browser access  | External service mapping |
```
---

# 🧠 Key Takeaways

✔ NodePort exposes app outside cluster
✔ ExternalName creates DNS alias
✔ ExternalName does NOT route traffic
✔ NodePort opens port on all nodes

---

# 🧾 One-Line Summary

```text id="6z4k1y"
NodePort exposes services externally via node ports, while ExternalName provides DNS-based access to external services.
```

---



# ☸️ Kubernetes Service – LoadBalancer (Detailed Notes)

---

![LoadBalancer](images/loadbalancer-service.jpg)



# 📌 What is LoadBalancer Service?

```text id="v3m1qa"
LoadBalancer is a Service type that exposes your application 
to the external world using a cloud provider load balancer.
```

---

# 🧠 Simple Understanding

```text id="p8x1zm"
User → LoadBalancer → Service → Pods
```

---

# 🎯 Why LoadBalancer?

## 🚫 Problem

```text id="n5y3rb"
❌ Pod IP cannot be accessed externally  
❌ ClusterIP is internal only  
❌ NodePort is not ideal for production
```

---

## ✅ Solution

```text id="j1q9kt"
Use LoadBalancer to expose application with a public IP
```

---

# 🔑 Key Features

* Provides **external/public IP**
* Automatically creates **cloud load balancer**
* Distributes traffic across multiple pods
* Works with cloud providers:

  * AWS (ELB)
  * GCP (GCLB)
  * Azure (ALB)

---

# 🔄 Flow Diagram

```text id="0g3n5x"
User (Browser)
      │
      ▼
Cloud LoadBalancer (Public IP)
      │
      ▼
Service (LoadBalancer)
      │
      ▼
Pod1   Pod2   Pod3
```

---

# 📄 LoadBalancer YAML Example

```yaml id="c4y2mn"
apiVersion: v1
kind: Service

metadata:
  name: mysvc              # service name

spec:
  type: LoadBalancer       # expose service externally using cloud LB

  selector:
    app: webserver         # connects to pods with this label

  ports:
    - port: 80             # service port (external access)
      targetPort: 80       # container port inside pod
```

---

# ⚙️ Create Service

```bash id="q7p2zt"
# create load balancer service
kubectl create -f service-lb.yml
```

---

# 🔍 Verify Service

```bash id="q4s8xa"
# check service details
kubectl get svc
```

Example output:

```text id="s9f1bn"
NAME     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)
mysvc    LoadBalancer   10.0.0.10      34.100.20.10    80:30007/TCP
```

---

# 🌐 Access Application

```text id="c7k2op"
http://<EXTERNAL-IP>
```

Example:

```text id="k8n3wl"
http://34.100.20.10
```

---

# ⚠️ Important Notes

* Works only in **cloud environments**
* On local (Minikube, Docker Desktop):

  * External IP may show as **pending**
* Behind the scenes:

```text id="w5j6fa"
LoadBalancer → NodePort → Pods
```

---

# ⚖️ Comparison with Other Services
```
| Feature        | ClusterIP         | NodePort           | LoadBalancer         |
| -------------- | ----------------- | ------------------ | -------------------- |
| Access         | Internal          | External (Node IP) | External (Public IP) |
| Use Case       | Pod communication | Testing            | Production           |
| Load Balancing | Internal          | Basic              | Advanced             |
```
---

# 🧠 Key Takeaways

✔ Best for production external access
✔ Provides public IP
✔ Automatically creates cloud load balancer
✔ Handles traffic distribution

---

# 🧾 One-Line Summary

```text id="m9c2zs"
LoadBalancer exposes Kubernetes services to the internet using a cloud-managed load balancer with a public IP.
```

---


# Kubernetes Ingress with NGINX Ingress Controller — Complete Notes

# What is Ingress in Kubernetes?

An **Ingress** is a Kubernetes object used to expose HTTP/HTTPS applications from outside the cluster.

Instead of exposing every application using:
- NodePort
- LoadBalancer

Ingress provides:
- Host-based routing
- Path-based routing
- Centralized traffic management

Example:
```
| Domain | Target Service |
|---|---|
| website01.example.com | service1 |
| website02.example.com | service2 |
```
Ingress works like a **reverse proxy**.

---

# Architecture Flow

```text
Browser/User
      |
      v
NGINX Ingress Controller
      |
 -------------------------
 |                       |
 v                       v
service1             service2
 |                       |
 v                       v
pod1                  pod2
```
![Ingress Service](images/ingress-service.jpg)
---

# Step 1 — Install NGINX Ingress Controller

## What is Ingress Controller?

Ingress resource alone cannot route traffic.

We need an actual controller that:
- Reads Ingress rules
- Configures reverse proxy
- Routes traffic

Here we use:
- NGINX Ingress Controller

---

## Official Documentation

https://github.com/kubernetes/ingress-nginx/blob/main/docs/deploy/index.md

---

## Install Command

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.1/deploy/static/provider/cloud/deploy.yaml
```

---

## Verify Installation

```bash
kubectl get pods -n ingress-nginx
```

---

# Step 2 — Create Backend Pods

```bash
kubectl run pod1 --image nginx
kubectl run pod2 --image nginx
```

---

## Verify Pods

```bash
kubectl get pods
```

---

# Step 3 — Create Services

## Create Service for pod1

```bash
kubectl expose pod pod1 --name service1 --port=80 --target-port=80
```

---

## Create Service for pod2

```bash
kubectl expose pod pod2 --name service2 --port=80 --target-port=80
```

---

# Step 4 — Verify Services

```bash
kubectl get svc
```

Example:

```text
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)
service1     ClusterIP   10.8.4.0       <none>        80/TCP
service2     ClusterIP   10.8.2.113     <none>        80/TCP
```

---

# Important Concepts

## ClusterIP

Default Kubernetes service type.

Accessible:
- Only inside cluster

---

# Step 5 — Create Frontend Test Pod

```bash
kubectl run frontend-pod --image ubuntu --command -- sleep 36000
```

---

# Step 6 — Enter Frontend Pod

```bash
kubectl exec -it frontend-pod -- bash
```

---

# Step 7 — Install Utilities

```bash
apt-get update
apt-get install curl nano -y
```

---

# Step 8 — Test Services Internally

```bash
curl service1
curl service2
```

---

# Step 9 — Create Ingress Resource

Create file:

```bash
vim ingress.yml
```

---

# Ingress YAML

```yaml
# API version for Ingress resource
apiVersion: networking.k8s.io/v1

# Kubernetes resource type
kind: Ingress

metadata:

  # Name of the Ingress resource
  name: name-virtual-host-ingress

spec:

  # Specifies which Ingress controller should handle this Ingress
  ingressClassName: nginx

  # Rules define routing behavior
  rules:

  # First virtual host configuration
  - host: website01.example.com

    # HTTP routing rules
    http:

      # List of URL paths
      paths:

      # Route all traffic starting with "/"
      - path: /

        # Prefix means match all URLs beginning with "/"
        pathType: Prefix

        # Backend service configuration
        backend:

          service:

            # Service name to forward traffic
            name: service1

            # Service port number
            port:
              number: 80

  # Second virtual host configuration
  - host: website02.example.com

    # HTTP routing rules
    http:

      # List of URL paths
      paths:

      # Route all traffic starting with "/"
      - path: /

        # Prefix means match all URLs beginning with "/"
        pathType: Prefix

        # Backend service configuration
        backend:

          service:

            # Service name to forward traffic
            name: service2

            # Service port number
            port:
              number: 80
```

---

# Step 10 — Create Ingress

```bash
kubectl apply -f ingress.yml
```

---

# Step 11 — Verify Ingress

```bash
kubectl get ingress
```

---

# Step 12 — Describe Ingress

```bash
kubectl describe ingress name-virtual-host-ingress
```

---

# Step 13 — Check IngressClass

```bash
kubectl get ingressclass
```

Expected:

```text
NAME    CONTROLLER
nginx   k8s.io/ingress-nginx
```

---

# Step 14 — Check Ingress Controller Service

```bash
kubectl get svc -n ingress-nginx
```

---

# Step 15 — Copy LoadBalancer External IP

Example:

```text
34.135.243.240
```

---

# Step 16 — Update Frontend Pod Hosts File

```bash
kubectl exec -it frontend-pod -- bash
```

Open hosts file:

```bash
nano /etc/hosts
```

Add entry:

```text
34.135.243.240 website01.example.com website02.example.com
```

---

# Step 17 — Test Ingress

```bash
curl website01.example.com
curl website02.example.com
```

---

# Step 18 — Customize Website Content

## Update pod1

```bash
kubectl exec -it pod1 -- bash
cd /usr/share/nginx/html
echo "this is website1" > index.html
```

---

## Update pod2

```bash
kubectl exec -it pod2 -- bash
cd /usr/share/nginx/html
echo "this is website2" > index.html
```

---

# Step 19 — Verify Again

```bash
curl website01.example.com
```

Output:

```text
this is website1
```

---

```bash
curl website02.example.com
```

Output:

```text
this is website2
```

---

# Step 20 — Access from Windows Browser

Open Notepad as Administrator.

Open file:

```text
C:\Windows\System32\Drivers\etc\hosts
```

Add:

```text
34.135.243.240 website01.example.com website02.example.com
```

Save file.

---

# Final Flow

```text
Browser
   |
   v
website01.example.com
   |
   v
LoadBalancer External IP
   |
   v
NGINX Ingress Controller
   |
   v
Ingress Rules
   |
   v
service1
   |
   v
pod1
```

---

# Important Kubernetes Concepts Covered
```
| Concept | Purpose |
|---|---|
| Pod | Runs container |
| Service | Stable networking |
| ClusterIP | Internal communication |
| Ingress | HTTP/HTTPS routing |
| Ingress Controller | Implements ingress rules |
| LoadBalancer | Public external access |
| DNS/Hosts file | Hostname mapping |
```
---

# Useful Commands Summary

## Pods

```bash
kubectl get pods
```

## Services

```bash
kubectl get svc
```

## Ingress

```bash
kubectl get ingress
```

## Describe Ingress

```bash
kubectl describe ingress name-virtual-host-ingress
```

## Ingress Controller Namespace

```bash
kubectl get pods -n ingress-nginx
```


# Kubernetes ReplicaSet and Deployment — Complete Notes

## What is a ReplicaSet?

A ReplicaSet ensures that a specified number of pod replicas are always running.

### Example

If you want 3 nginx pods:
- ReplicaSet continuously monitors pods
- If one pod crashes, ReplicaSet automatically creates another pod

## Why ReplicaSet is Important?

### Without ReplicaSet
- Pods may fail permanently
- Manual recreation needed

### With ReplicaSet
- High availability
- Self-healing
- Automatic pod replacement

## ReplicaSet Architecture

```text
ReplicaSet
     |
 ---------------------
 |         |         |
Pod1      Pod2      Pod3
```

If Pod2 crashes:

```text
ReplicaSet
     |
 ---------------------
 |         |         |
Pod1      X        Pod3
              |
          New Pod2 Created
```

---

# ReplicaSet YAML File

Create file:

```bash
vim replicaset.yml
```

## ReplicaSet YAML with Comments

```yaml
# Resource type
kind: ReplicaSet

# API version for ReplicaSet
apiVersion: apps/v1

metadata:
  # Name of the ReplicaSet
  name: myrs

spec:

  # Desired number of pod replicas
  replicas: 3

  # Selector identifies which pods belong to this ReplicaSet
  selector:

    # Match pods having this label
    matchLabels:
      app: webserver

  # Template used to create new pods
  template:

    metadata:

      # Labels assigned to created pods
      labels:
        app: webserver

    spec:

      containers:

        # Container name
        - name: c1

          # Container image
          image: nginx
```

# Important Fields Explanation

## replicas

```yaml
replicas: 3
```

Means:
- Maintain exactly 3 pods

If:
- One pod dies → new pod created
- Extra pod created manually → ReplicaSet removes extra pod

## selector

```yaml
selector:
  matchLabels:
    app: webserver
```

ReplicaSet identifies pods using labels.

Only pods matching:

```yaml
app: webserver
```

are managed.

## template

Defines:
- How new pods should be created

Contains:
- Labels
- Containers
- Images
- Ports
- Environment variables

---

# Create ReplicaSet

```bash
kubectl create -f replicaset.yml
```

# Verify Resources

```bash
kubectl get all
```

# Get Only Webserver Pods

```bash
kubectl get pods -l app=webserver
```

## What is -l?

`-l` means:
- Label selector

Example:

```bash
-l app=webserver
```

Shows only pods with:
```yaml
app: webserver
```

---

# Scaling ReplicaSet

## Scale Up

```bash
kubectl scale replicaset myrs --replicas=5
```

Now ReplicaSet creates:
- 5 pods

## Scale Down

```bash
kubectl scale replicaset myrs --replicas=2
```

ReplicaSet removes extra pods.

---

# Self-Healing Demo

Delete one pod:

```bash
kubectl delete pod <pod-name>
```

Immediately:
- ReplicaSet creates another pod automatically

This is called:
- Self-healing

---

# Kubernetes Explain Command

## Explain Pod Fields

```bash
kubectl explain Pod
```

Shows:
- Pod structure
- YAML fields
- Descriptions

## Explain ReplicaSet Fields

```bash
kubectl explain ReplicaSet
```

## Explain Nested Fields

```bash
kubectl explain deployment.spec
```

OR

```bash
kubectl explain deployment.spec.template
```

Very useful for learning YAML.

---

# What is a Deployment?

Deployment is a higher-level Kubernetes object.

Deployment manages:
- ReplicaSets
- Rolling updates
- Rollbacks
- Application versioning

---

# Deployment Architecture

```text
Deployment
     |
ReplicaSet
     |
Pods
```

# Why Use Deployment Instead of ReplicaSet?

ReplicaSet only:
- Maintains pod count

Deployment provides:
- Rolling updates
- Rollbacks
- Version control
- Zero downtime deployment

---

# Rolling Update Deployment

A rolling update updates pods gradually.

Example:
- Old version removed one by one
- New version added one by one

No downtime.

---

# Deployment YAML File

Create file:

```bash
vim deployment.yml
```

## Deployment YAML with Comments

```yaml
# Resource type
kind: Deployment

# API version
apiVersion: apps/v1

metadata:

  # Deployment name
  name: kubeserve

spec:

  # Desired number of pods
  replicas: 3

  # Wait time before marking pod as ready
  minReadySeconds: 10

  # Deployment strategy
  strategy:

    # Rolling update strategy
    type: RollingUpdate

    rollingUpdate:

      # Maximum unavailable pods during update
      maxUnavailable: 1

      # Extra temporary pods allowed during update
      maxSurge: 1

  # Select pods using labels
  selector:

    matchLabels:
      app: kubeserve

  # Pod template
  template:

    metadata:

      # Pod labels
      labels:
        app: kubeserve

    spec:

      containers:

        # Container name
        - name: app

          # Docker image
          image: leaddevops/kubeserve:v1
```

# Important Deployment Concepts

## minReadySeconds

```yaml
minReadySeconds: 10
```

Kubernetes waits:
- 10 seconds before considering pod healthy

Then only next pod update happens.

## maxUnavailable

```yaml
maxUnavailable: 1
```

Maximum pods allowed unavailable during update.

Example:
- Total pods = 3
- At most 1 pod can go down

## maxSurge

```yaml
maxSurge: 1
```

Allows:
- 1 extra pod temporarily during update

Example:
- Desired pods = 3
- During update = 4 pods temporarily

---

# Rolling Update Process

Example:

```text
Current:
v1 v1 v1

Update to v2:
v2 v1 v1
v2 v2 v1
v2 v2 v2
```

Gradual replacement.

---

# Create Deployment

```bash
kubectl create -f deployment.yml
```

# Verify Deployment

```bash
kubectl get deployment
```

# Get All Resources

```bash
kubectl get all
```

---

# Scale Deployment

## Scale Up

```bash
kubectl scale deployment kubeserve --replicas=5
```

## Scale Down

```bash
kubectl scale deployment kubeserve --replicas=2
```

---

# Update Application Image

## Upgrade to Version 2

```bash
kubectl set image deployment kubeserve app=leaddevops/kubeserve:v2
```

## What Happens Internally?

Deployment:
- Creates new ReplicaSet
- Gradually creates v2 pods
- Removes old v1 pods

---

# Check Rollout Status

```bash
kubectl rollout status deployment kubeserve
```

Shows:
- Update progress
- Success/failure

---

# Upgrade to Version 3

```bash
kubectl set image deployment kubeserve app=leaddevops/kubeserve:v3
```

# Rollback Deployment

Suppose:
- v3 is faulty

Rollback to previous version:

```bash
kubectl rollout undo deployment kubeserve
```

---

# View Rollout History

```bash
kubectl rollout history deployment kubeserve
```

# Check ReplicaSets Created by Deployment

```bash
kubectl get rs
```

Each update creates:
- New ReplicaSet

---

# Difference Between ReplicaSet and Deployment
```
| Feature | ReplicaSet | Deployment |
|---|---|---|
| Maintains pod count | Yes | Yes |
| Self healing | Yes | Yes |
| Scaling | Yes | Yes |
| Rolling updates | No | Yes |
| Rollbacks | No | Yes |
| Version control | No | Yes |
| Production usage | Rare | Common |
```
---

# Real-World Example

Suppose:
- Your app version v1 is running
- You release v2

Deployment:
- Updates pods gradually
- Avoids downtime
- Allows rollback if issue occurs

This is how production applications are updated.

---

# Useful Commands Summary

## ReplicaSet Commands

### Create ReplicaSet

```bash
kubectl create -f replicaset.yml
```

### Get ReplicaSets

```bash
kubectl get rs
```

### Scale ReplicaSet

```bash
kubectl scale replicaset myrs --replicas=5
```

---

## Deployment Commands

### Create Deployment

```bash
kubectl create -f deployment.yml
```

### Get Deployments

```bash
kubectl get deployment
```

### Scale Deployment

```bash
kubectl scale deployment kubeserve --replicas=5
```

### Update Image

```bash
kubectl set image deployment kubeserve app=leaddevops/kubeserve:v2
```

### Check Rollout

```bash
kubectl rollout status deployment kubeserve
```

### Rollback

```bash
kubectl rollout undo deployment kubeserve
```

---

# Best Practices

- Use Deployment instead of direct ReplicaSet
- Always use labels properly
- Use rolling updates in production
- Monitor rollout status
- Use rollback during failures
- Store YAML files in Git repositories

# Kubernetes Horizontal Pod Autoscaler (HPA) — Complete Notes

## What is Horizontal Pod Autoscaler (HPA)?

Horizontal Pod Autoscaler (HPA) automatically scales the number of pods in a deployment based on CPU or memory usage.

It helps Kubernetes applications handle:
- High traffic
- Low traffic
- Automatic scaling

---

# Main Topics Covered

- Pod Resources (CPU and Memory)
- Resource Requests and Limits
- Metrics Server
- Horizontal Scaling vs Vertical Scaling
- HPA Formula
- HPA Configuration
- Practical Demonstration
- Load Generator Testing
- Automatic Scaling Up and Down

---

# Pod Resources (CPU and Memory)

Containers inside pods consume:
- CPU
- Memory

Kubernetes allows us to control resource usage using:
- Requests
- Limits

---

# CPU in Kubernetes

CPU is measured in:
- CPU cores
- Milli CPU cores (m)

Examples:
```
| Value | Meaning |
|---|---|
| 1000m | 1 CPU core |
| 500m | 0.5 CPU |
| 100m | 0.1 CPU |
| 10m | 0.01 CPU |
```
Example:

```yaml
resources:
  limits:
    cpu: 10m
```

This means:
- Container can use maximum 10 milli CPU

---

# Memory in Kubernetes

Memory is measured in:
- Mi (Mebibytes)
- Gi (Gibibytes)

Examples:
```
| Value | Meaning |
|---|---|
| 128Mi | 128 MB |
| 1Gi | 1 GB |
``
---

# Resource Requests and Limits

## Requests

Requests mean:
- Minimum guaranteed resources

Example:

```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
```

---

## Limits

Limits mean:
- Maximum resources container can consume

Example:

```yaml
resources:
  limits:
    cpu: 500m
    memory: 256Mi
```

---

# Why Resource Limits Are Important?

Without limits:
- One pod may consume all CPU
- Other pods may become slow
- Node resources may get exhausted

This is called:
- Resource starvation

---

# Horizontal Scaling vs Vertical Scaling

## Horizontal Scaling

Horizontal scaling means:
- Creating more pods

Example:

```text
1 Pod → 5 Pods
```

---

## Vertical Scaling

Vertical scaling means:
- Increasing resources of existing pods

Example:

```text
CPU: 100m → 500m
Memory: 128Mi → 512Mi
```

---

# Metrics Server

Metrics Server provides:
- CPU metrics
- Memory metrics

for:
- Pods
- Nodes

HPA uses Metrics Server to monitor CPU usage.

---

# Install Metrics Server

## Install Metrics Server

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

## Download Patch File

```bash
wget -c https://gist.githubusercontent.com/initcron/1a2bd25353e1faa22a0ad41ad1c01b62/raw/008e23f9fbf4d7e2cf79df1dd008de2f1db62a10/k8s-metrics-server.patch.yaml
```

## Apply Patch

```bash
kubectl patch deploy metrics-server -p "$(cat k8s-metrics-server.patch.yaml)" -n kube-system
```

---

# Verify Metrics Server

```bash
kubectl get pods -n kube-system
```

---

# Create HPA YAML File

```bash
cd mykubefiles
vim hpa.yml
```

---

# HPA YAML with Comments

```yaml
# Deployment API version
apiVersion: apps/v1

# Kubernetes object type
kind: Deployment

metadata:

  # Deployment name
  name: nginx

  labels:

    # Label assigned to deployment
    app: nginx

spec:

  # Initial pod count
  replicas: 1

  # Select matching pods
  selector:

    # Match labels
    matchLabels:
      app: nginx

  # Pod template
  template:

    metadata:

      # Pod name
      name: nginxpod

      labels:

        # Pod label
        app: nginx

    spec:

      containers:

        # Container name
        - name: nginx

          # Docker image
          image: nginx:latest

          # Resource configuration
          resources:

            limits:

              # Maximum CPU usage allowed
              cpu: 10m

---

# Service API version
apiVersion: v1

# Kubernetes object type
kind: Service

metadata:

  # Service name
  name: nginx-svc

spec:

  # Internal cluster service
  type: ClusterIP

  # Connect service to matching pods
  selector:
    app: nginx

  ports:

   # Port configuration
   - protocol: TCP

     # Service port
     port: 80

     # Container port
     targetPort: 80

---

# HPA API version
apiVersion: autoscaling/v1

# Kubernetes object type
kind: HorizontalPodAutoscaler

metadata:

  # HPA name
  name: nginx-hpa

spec:

  # Deployment to monitor
  scaleTargetRef:

    # API version
    apiVersion: apps/v1

    # Resource type
    kind: Deployment

    # Deployment name
    name: nginx

  # Minimum pods allowed
  minReplicas: 1

  # Maximum pods allowed
  maxReplicas: 10

  # Target average CPU utilization percentage
  targetCPUUtilizationPercentage: 5
```

---

# Explanation for HPA

Deployment can:
- Scale pods manually

But Deployment alone cannot decide:
- When to scale automatically

Deployment needs CPU usage information.

Metrics Server:
- Collects CPU and memory metrics

HPA:
- Reads metrics from Metrics Server
- Decides whether to scale pods

---

# HPA Formula

Desired Count = Current Replicas × (Current CPU Utilization / Target CPU Utilization)

---

# Example Calculation

Suppose:
- Current replicas = 4
- Target CPU utilization = 50%
- Current CPU utilization = 60%

Formula:

Desired Count = 4 × (60 / 50)

Desired Count = 4.8

Rounded value:
- 5 Pods

HPA tells deployment:
- Scale replicas to 5

---

# CPU Utilization Example

Suppose:
- CPU limit = 500m
- Current usage = 300m

CPU utilization percentage:

(300 / 500) × 100 = 60%

---

# Create Resources

## Delete Existing Resources

```bash
kubectl delete all --all
```

## Create HPA Resources

```bash
kubectl create -f hpa.yml
```

## Verify Resources

```bash
kubectl get all
```

---

# Launch Load Generator Pod

Open another terminal and run:

```bash
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://Service-Internal-IP:80; done"
```

---

# What Load Generator Does

This command:
- Continuously sends traffic
- Increases CPU usage
- Triggers autoscaling

---

# Monitor Pods

## Check Pods

```bash
kubectl get pods
```

## Check CPU Usage

```bash
kubectl top pods
```

---

# Observe Scaling

## Scaling Up

When CPU usage increases:
- HPA creates more pods

Example:

```text
1 Pod → 3 Pods → 5 Pods
```

---

## Scaling Down

When traffic decreases:
- CPU usage decreases
- HPA removes extra pods

Example:

```text
5 Pods → 3 Pods → 1 Pod
```

---

# Important HPA Parameters
```
| Parameter | Meaning |
|---|---|
| minReplicas | Minimum pods |
| maxReplicas | Maximum pods |
| targetCPUUtilizationPercentage | Target CPU percentage |
```
---

# HPA Workflow

```text
User Traffic Increases
          ↓
CPU Usage Increases
          ↓
Metrics Server Collects Metrics
          ↓
HPA Reads Metrics
          ↓
HPA Calculates Desired Pods
          ↓
Deployment Scales Pods
```

---

# Useful Commands

## View HPA

```bash
kubectl get hpa
```

## Describe HPA

```bash
kubectl describe hpa nginx-hpa
```

## View Pod Metrics

```bash
kubectl top pods
```

## View Node Metrics

```bash
kubectl top nodes
```

---

# Real-World Example

Suppose:
- Website traffic suddenly increases

Without HPA:
- Pods become overloaded
- Website becomes slow

With HPA:
- Kubernetes automatically creates more pods
- Traffic gets distributed
- Website remains stable

---

# Best Practices

- Always configure resource limits
- Install Metrics Server correctly
- Use HPA in production
- Set reasonable minReplicas and maxReplicas
- Monitor scaling behavior regularly
- Test autoscaling using load generators

---

# Summary

HPA automatically:
- Scales up during high traffic
- Scales down during low traffic

Main components:
- Deployment
- Metrics Server
- HPA

Benefits:
- Better performance
- High availability
- Efficient resource usage




# Vertical Pod Autoscaling (VPA) in Kubernetes

## What is VPA?

Vertical Pod Autoscaling (VPA) automatically adjusts the CPU and memory resource requests/limits for containers running inside pods.

Instead of increasing the number of pods like HPA, VPA increases or decreases the resources assigned to existing pods.

---

# How VPA Works

VPA works in 3 major steps:

## 1. Monitor Resource Usage

VPA continuously monitors:
- CPU usage
- Memory usage

of running pods.

---

## 2. Recommend Resources

Based on usage statistics, VPA calculates:
- Recommended CPU
- Recommended Memory

for the container.

---

## 3. Apply Changes

VPA updates pod resource requests automatically.

Usually, pods are restarted so new resource values can take effect.

---

# Advantages of VPA

## Resource Optimization

Prevents:
- Over-provisioning
- Under-provisioning

---

## Simplified Resource Management

No need to manually adjust CPU/memory requests repeatedly.

---

## Better Performance

Applications get resources dynamically according to real usage.

---

# Step 1: Install VPA

## Clone Kubernetes Autoscaler Repository

```bash
git clone https://github.com/kubernetes/autoscaler.git
```

---

## Navigate to Autoscaler Directory

```bash
cd autoscaler
```

---

## Install VPA Components

```bash
# Install VPA components
./vertical-pod-autoscaler/hack/vpa-up.sh

# Generate certificates for admission controller
./vertical-pod-autoscaler/pkg/admission-controller/gencerts.sh
```

These commands install:
- VPA controllers
- CRDs
- Admission controllers
- Recommenders

---

# Verify Installation

```bash
kubectl get pods -n kube-system | grep vpa
```

Example:

```bash
vpa-admission-controller
vpa-recommender
vpa-updater
```

---

# Step 2: Create Deployment

Create file:

```bash
vim vpa-deployment.yaml
```

---

# Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: high-cpu-utilization-deployment

spec:
  replicas: 2

  selector:
    matchLabels:
      app: cpu-utilization-app

  template:
    metadata:
      labels:
        app: cpu-utilization-app

    spec:
      containers:
        - name: cpu-utilization-container

          # Ubuntu image
          image: ubuntu

          # Install stress-ng and generate CPU load continuously
          command:
            [
              "/bin/sh",
              "-c",
              "apt-get update && apt-get install -y stress-ng && while true; do stress-ng --cpu 1; done"
            ]

          resources:
            requests:
              # Minimum requested CPU
              cpu: "0.05"

            limits:
              # Maximum CPU allowed
              cpu: "0.05"
```

---

# Apply Deployment

```bash
kubectl apply -f vpa-deployment.yaml
```

---

# Step 3: Create VPA Object

Create file:

```bash
vim vpa.yaml
```

---

# VPA YAML

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler

metadata:
  name: stress-vpa

spec:
  targetRef:
    # Target deployment managed by VPA
    apiVersion: "apps/v1"
    kind: Deployment
    name: high-cpu-utilization-deployment

  updatePolicy:
    # Automatically update pod resources
    updateMode: Auto

  resourcePolicy:
    containerPolicies:
      - containerName: "*"

        minAllowed:
          # Minimum CPU allowed
          cpu: 100m

          # Minimum memory allowed
          memory: 50Mi

        maxAllowed:
          # Maximum CPU allowed
          cpu: 200m

          # Maximum memory allowed
          memory: 500Mi

        controlledResources:
          # Resources controlled by VPA
          - cpu
          - memory
```

---

# Apply VPA

```bash
kubectl apply -f vpa.yaml
```

---

# Monitor Pods

## Check CPU Usage

```bash
kubectl top po
```

Example:

```bash
NAME                                               CPU(cores)   MEMORY(bytes)
high-cpu-utilization-deployment-78cc948dfb-fqbq9   50m          13Mi
high-cpu-utilization-deployment-78cc948dfb-qtbt8   50m          9Mi
```

---

# Check CPU Requests

```bash
kubectl get po -o jsonpath='{.items[*].spec.containers[*].resources.requests.cpu}'
```

Output:

```bash
50m 50m
```

Initially both pods request only `50m`.

---

# Check VPA Status

```bash
kubectl get vpa
```

Example:

```bash
NAME        MODE   CPU    MEM
stress-vpa  Auto   100m   262144k
```

---

# Updated CPU Requests

```bash
kubectl get po -o jsonpath='{.items[*].spec.containers[*].resources.requests.cpu}'
```

Output:

```bash
100m 100m
```

VPA increased CPU requests from:
- 50m → 100m

because of:

```yaml
minAllowed:
  cpu: 100m
```

---

# Why CPU Became 126m Instead of 200m?

Because VPA:
- Uses actual usage data
- Avoids over-provisioning
- Gradually increases resources

Even though maximum allowed is:

```yaml
maxAllowed:
  cpu: 200m
```

VPA only assigns what is necessary.

---

# Exclude a Specific Container from VPA

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler

spec:
  resourcePolicy:
    containerPolicies:
      - containerName: my-container

        # Disable VPA for this container
        mode: "Off"
```

---

# HPA vs VPA
```
| Feature | HPA | VPA |
|---|---|---|
| Scaling Type | Horizontal | Vertical |
| Method | Adds more pods | Increases pod resources |
| Best For | Stateless apps | Stateful apps |
| Scaling | Pod count | CPU/Memory |
```
---

# When to Use HPA

Use HPA when:
- Application is stateless
- Traffic varies heavily
- Easy to add more pods

Examples:
- Web applications
- API servers

---

# When to Use VPA

Use VPA when:
- Application is stateful
- Difficult to scale horizontally
- Resource usage changes over time

Examples:
- Databases
- Monitoring systems
- Stateful services


# Resource Quotas in Kubernetes

## What is a ResourceQuota?

A `ResourceQuota` in Kubernetes is a namespace-level object that limits how many resources a namespace can consume.

It helps control:

- CPU usage
- Memory usage
- Number of pods
- PVCs
- Services
- Other Kubernetes objects

Resource Quotas are mainly used in multi-team or multi-tenant Kubernetes clusters.

---

# Why Use Resource Quotas?

Without quotas:

- A badly configured application may consume all cluster resources.
- One namespace may create too many pods.
- Cluster performance may become unstable.

With quotas:

- Resource usage is controlled.
- Teams get fair resource allocation.
- Cluster stability improves.

---

# Main Purpose of ResourceQuota

ResourceQuota helps to:

- Prevent resource exhaustion
- Enforce fair usage
- Improve cluster management
- Control namespace resource consumption

---

# Demo 1: ResourceQuota Example

Suppose we want a namespace called `dev-quota` with:

- Maximum 2 CPUs
- Maximum 4Gi memory
- Maximum 10 pods
- CPU and memory request/limit enforcement

---

# Create ResourceQuota YAML

```bash
# Create ResourceQuota YAML file
vim resourcequota-demo.yml
```

---

# ResourceQuota YAML

```yaml
apiVersion: v1
kind: ResourceQuota

metadata:
  # Name of the ResourceQuota object
  name: dev-quota

  # Namespace where quota is applied
  namespace: dev-quota

spec:
  hard:

    # Total CPU requests allowed in namespace
    requests.cpu: "2"

    # Total memory requests allowed in namespace
    requests.memory: "2Gi"

    # Maximum total CPU limits allowed
    limits.cpu: "2"

    # Maximum total memory limits allowed
    limits.memory: "4Gi"

    # Maximum number of pods allowed
    pods: "10"
```

---

# Apply ResourceQuota

```bash
# Apply ResourceQuota configuration
kubectl apply -f resource-quota.yaml
```

---

# Verify ResourceQuota

```bash
# Describe the ResourceQuota
kubectl describe quota dev-quota -n dev
```

---

# Explanation of ResourceQuota Fields

## requests.cpu

```yaml
requests.cpu: "2"
```

This is the total CPU requested by all pods inside the namespace.

---

## limits.cpu

```yaml
limits.cpu: "2"
```

This is the maximum CPU limit allowed for all pods combined.

---

## requests.memory

```yaml
requests.memory: "2Gi"
```

Total memory requested by all pods.

---

## limits.memory

```yaml
limits.memory: "4Gi"
```

Maximum memory limit allowed for all pods combined.

---

## pods

```yaml
pods: "10"
```

Maximum number of pods allowed in the namespace.

---

# What Happens if Quota is Exceeded?

If a deployment tries to:

- Launch an 11th pod
- Request more CPU
- Request more memory

Kubernetes will reject the request immediately.

You must either:

- Increase the quota
- Scale down existing workloads

---

# Demo 2: ResourceQuota with Deployment

---

# Create Namespace

```bash
# Create namespace
kubectl create namespace dev-team
```

---

# Define ResourceQuota

We will allow:

- Maximum 5 pods
- CPU requests: 250m
- Memory requests: 1Gi
- CPU limits: 500m
- Memory limits: 2Gi

---

# Create ResourceQuota File

```bash
# Create YAML file
vim resource-quota2.yml
```

---

# ResourceQuota YAML

```yaml
apiVersion: v1
kind: ResourceQuota

metadata:
  # ResourceQuota name
  name: dev-resource-quota

  # Namespace where quota is applied
  namespace: dev-team

spec:
  hard:

    # Maximum pods allowed
    pods: "5"

    # Total CPU requests allowed
    requests.cpu: "250m"

    # Total memory requests allowed
    requests.memory: "1Gi"

    # Total CPU limits allowed
    limits.cpu: "500m"

    # Total memory limits allowed
    limits.memory: "2Gi"
```

---

# Apply ResourceQuota

```bash
# Apply ResourceQuota
kubectl apply -f resource-quota2.yml
```

---

# Create Deployment

Each pod will use:

## Requests

- CPU: 50m
- Memory: 256Mi

## Limits

- CPU: 100m
- Memory: 512Mi

We will create 2 pods.

Total usage becomes:
```
| Resource | Total |
|---|---|
| CPU Requests | 100m |
| Memory Requests | 512Mi |
| CPU Limits | 200m |
| Memory Limits | 1Gi |
```
This is within the quota.

---

# Create Deployment YAML

```bash
# Create deployment YAML
vim deployment-resource.yml
```

---

# Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: nginx-deployment

  # Namespace for deployment
  namespace: dev-team

spec:
  # Number of pods
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

          # NGINX image
          image: nginx

          resources:

            requests:
              # Requested CPU
              cpu: "50m"

              # Requested memory
              memory: "256Mi"

            limits:
              # Maximum CPU allowed
              cpu: "100m"

              # Maximum memory allowed
              memory: "512Mi"
```

---

# Apply Deployment

```bash
# Apply deployment
kubectl apply -f deployment-resource.yml
```

---

# Verify Quota Usage

```bash
# Check quota usage
kubectl describe quota dev-resource-quota -n dev-team
```

You should see current resource usage within quota limits.

---

# Try to Break the Quota

Now scale the deployment to 6 replicas.

Example:

```bash
# Scale deployment to 6 pods
kubectl scale deployment nginx-deployment --replicas=6 -n dev-team
```

You may see an error like:

```bash
pods "deploymentName" is forbidden: exceeded quota: dev-resource-quota
```

---

# Why Did it Fail?

Because:

## CPU Request Calculation

```text
6 pods × 50m CPU = 300m CPU
```

But quota allows only:

```text
250m CPU
```

---

## Memory Request Calculation

```text
6 pods × 256Mi = 1.5Gi
```

But quota allows only:

```text
1Gi memory
```

Therefore Kubernetes rejects pod creation.

---

# Resource Usage Table
```text
| Limit Type      | Per Pod | 2 Pods Total | Quota Limit |
|-----------------|---------|--------------|-------------|
| Requests CPU    | 50m     | 100m         | 250m        |
| Requests Memory | 256Mi   | 512Mi        | 1Gi         |
| Limits CPU      | 100m    | 200m         | 500m        |
| Limits Memory   | 512Mi   | 1Gi          | 2Gi         |
```
---

# View Namespace Events

```bash
# View events sorted by timestamp
kubectl get events -n dev-team --sort-by='.lastTimestamp'
```

This helps identify:

- Which controller tried creating pods
- Why pod creation failed
- Requested vs allowed resources

---

# Describe Deployment

```bash
# Describe deployment
kubectl describe deployment nginx-deployment -n dev-team
```

This shows quota-related errors and events.

---

# Important Concept

ResourceQuota is enforced by the Kubernetes API Server.

If quota is exceeded:

- Pod creation is rejected immediately
- Pod is never scheduled
- Scheduler is not involved

---

# ResourceQuota Metrics in Prometheus & Grafana

Useful metrics from `kube-state-metrics`:

---

# kube_resourcequota

Shows:

- Current quota usage
- Hard limits

---

# kube_resourcequota_status_hard

Shows configured hard limits for:

- CPU
- Memory
- Pods
- PVCs

---

# kube_resourcequota_status_used

Shows currently used resources inside the namespace.

---

# Summary

ResourceQuota helps:

- Prevent resource overconsumption
- Control namespace usage
- Improve cluster stability
- Enforce fair resource allocation

It is extremely useful in:

- Multi-team clusters
- Shared Kubernetes environments
- Production clusters


# LimitRanges in Kubernetes

## What is LimitRange?

`LimitRange` is a Kubernetes resource used to define:

- Default CPU requests
- Default memory requests
- Default CPU limits
- Default memory limits
- Minimum resource values
- Maximum resource values

for containers or pods inside a namespace.

---

# Why Use LimitRange?

Without LimitRange:

- Pods may run without limits
- Containers may consume unlimited CPU/memory
- Other applications may suffer resource starvation

With LimitRange:

- Default values are assigned automatically
- Resource usage becomes controlled
- Teams follow standard resource policies

---

# Create Namespace

```bash
# Create namespace
kubectl create namespace dev-team
```

---

# Create LimitRange YAML

```bash
# Create LimitRange YAML file
vim limitrange.yml
```

---

# LimitRange YAML Example

```yaml
apiVersion: v1
kind: LimitRange

metadata:
  # Name of LimitRange object
  name: default-limits

  # Namespace where LimitRange is applied
  namespace: dev-team

spec:
  limits:
    - type: Container

      default:
        # Default CPU limit if container does not specify one
        cpu: "100m"

        # Default memory limit if container does not specify one
        memory: "256Mi"

      defaultRequest:
        # Default CPU request if not specified
        cpu: "50m"

        # Default memory request if not specified
        memory: "128Mi"

      min:
        # Minimum CPU allowed
        cpu: "10m"

        # Minimum memory allowed
        memory: "64Mi"

      max:
        # Maximum CPU allowed
        cpu: "500m"

        # Maximum memory allowed
        memory: "512Mi"
```

---

# Apply LimitRange

```bash
# Apply LimitRange configuration
kubectl apply -f limitrange.yml
```

---

# What Happens After Applying LimitRange?

Now if a container is created without specifying:

- CPU requests
- Memory requests
- CPU limits
- Memory limits

Kubernetes automatically assigns default values from LimitRange.

---

# Example Pod Without Requests/Limits

```yaml
apiVersion: v1
kind: Pod

metadata:
  # Pod name
  name: test-pod

  # Namespace
  namespace: dev-team

spec:
  containers:
    - name: nginx

      # NGINX image
      image: nginx
```

---

# Create Pod

```bash
# Create pod
kubectl apply -f pod-limit.yml
```

---

# Describe Pod

```bash
# Describe pod details
kubectl describe pod test-pod -n dev-team
```

Under the container section, Kubernetes automatically adds:

- Default CPU request
- Default memory request
- Default CPU limit
- Default memory limit

from the LimitRange configuration.

---

# Using ResourceQuota and LimitRange Together

ResourceQuota controls:

- Total namespace resource usage
- Total CPU usage
- Total memory usage
- Total pod count

LimitRange controls:

- Default resource values
- Minimum resource values
- Maximum resource values
- Per-container resource policies

---

# ResourceQuota YAML

```yaml
apiVersion: v1
kind: ResourceQuota

metadata:
  # ResourceQuota name
  name: dev-quota

  # Namespace where quota is applied
  namespace: dev-team

spec:
  hard:

    # Maximum number of pods
    pods: "5"

    # Total CPU requests allowed
    requests.cpu: "500m"

    # Total memory requests allowed
    requests.memory: "1Gi"

    # Total CPU limits allowed
    limits.cpu: "1"

    # Total memory limits allowed
    limits.memory: "2Gi"
```

---

# LimitRange YAML

```yaml
apiVersion: v1
kind: LimitRange

metadata:
  # LimitRange name
  name: dev-limits

  # Namespace where LimitRange is applied
  namespace: dev-team

spec:
  limits:
    - type: Container

      default:
        # Default CPU limit
        cpu: "100m"

        # Default memory limit
        memory: "256Mi"

      defaultRequest:
        # Default CPU request
        cpu: "50m"

        # Default memory request
        memory: "128Mi"

      min:
        # Minimum CPU allowed
        cpu: "10m"

        # Minimum memory allowed
        memory: "64Mi"

      max:
        # Maximum CPU allowed
        cpu: "300m"

        # Maximum memory allowed
        memory: "512Mi"
```

---

# Pod Exceeding Maximum Limits

```yaml
apiVersion: v1
kind: Pod

metadata:
  # Pod name
  name: bad-pod

  # Namespace
  namespace: dev-team

spec:
  containers:
    - name: nginx

      # NGINX image
      image: nginx

      resources:

        requests:
          # Requested CPU exceeds max
          cpu: "500m"

          # Requested memory exceeds max
          memory: "1Gi"

        limits:
          # CPU limit exceeds allowed max
          cpu: "600m"

          # Memory limit exceeds allowed max
          memory: "1.5Gi"
```

---

# Create Bad Pod

```bash
# Try creating pod
kubectl create -f bad-pod.yml
```

---

# Expected Error

```bash
Error from server (Forbidden): pods "bad-pod" is forbidden: exceeded the max cpu limit per container
```

---

# Difference Between ResourceQuota and LimitRange
```
| Feature | ResourceQuota | LimitRange |
|---|---|---|
| Scope | Namespace total usage | Per container/pod |
| Controls | Total resources | Default/min/max resources |
| Purpose | Prevent namespace overuse | Standardize container resources |
```
---

# Summary

LimitRange helps:

- Automatically assign default resources
- Prevent containers from using excessive resources
- Enforce min/max resource rules
- Improve cluster fairness and stability

Combined with ResourceQuota, it provides strong resource management in Kubernetes.


# Kubernetes Deployment Strategies

This document explains the deployment strategies shown in the images.

---

# 1. Recreate Deployment Strategy

## Concept

In Recreate strategy:

- Old version pods (v1) are deleted first
- New version pods (v2) are created afterward

---

# Flow

```text
Delete Old Pods (v1)
        ↓
Application Downtime
        ↓
Create New Pods (v2)
```

---

# Example from Image

Suppose:

- v1 pods are currently running
- You want to deploy v2

Kubernetes will:

1. Delete all v1 pods
2. Create v2 pods

---

# Important Point

Because old pods are deleted before new pods are ready:

## Downtime occurs

Users cannot access the application temporarily.

---

# Advantages

- Simple deployment strategy
- Easy to configure
- No compatibility issues between versions

---

# Disadvantages

- Causes downtime
- Not suitable for production critical applications

---

# Best Use Cases

Use Recreate strategy for:

- Development environments
- Testing environments
- Non-critical applications

---

# Recreate Deployment YAML Example

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: recreate-demo

spec:
  replicas: 3

  strategy:
    # Recreate deployment strategy
    type: Recreate

  selector:
    matchLabels:
      app: myapp

  template:
    metadata:
      labels:
        app: myapp

    spec:
      containers:
        - name: app

          # Application image
          image: nginx
```

---

# 2. Rolling Update Strategy

## Concept

Rolling Update is the default deployment strategy in Kubernetes.

Instead of deleting all old pods together:

- One new pod is created
- One old pod is deleted

This process continues gradually.

---

# Flow

```text
Create New Pod
      ↓
Delete Old Pod
      ↓
Repeat Until Update Completes
```

---

# Important Parameters

## maxUnavailable

Defines:

How many old pods can be unavailable during deployment.

Example:

```yaml
maxUnavailable: 1
```

Means:

Only 1 old pod can be unavailable at a time.

---

## maxSurge

Defines:

How many extra pods can be created temporarily.

Example:

```yaml
maxSurge: 1
```

Means:

Kubernetes can create 1 extra pod during update.

---

# Advantages

- No downtime
- Built into Kubernetes
- Safer than recreate strategy
- Good for most applications

---

# Disadvantages

- Old and new versions run simultaneously
- Possible compatibility issues
- Slower deployment process

---

# Rolling Update YAML Example

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: rolling-update-demo

spec:
  replicas: 3

  strategy:
    type: RollingUpdate

    rollingUpdate:
      # Maximum unavailable pods
      maxUnavailable: 1

      # Extra pods created during update
      maxSurge: 1

  selector:
    matchLabels:
      app: myapp

  template:
    metadata:
      labels:
        app: myapp

    spec:
      containers:
        - name: app

          # Application image
          image: nginx
```

---

# Example Explanation

Suppose:

Current pods:

```text
v1 v1 v1
```

During update:

```text
v1 v1 v2
```

Then:

```text
v1 v2 v2
```

Finally:

```text
v2 v2 v2
```

---

# 3. Blue-Green Deployment

## Concept

Blue-Green deployment maintains:

- Two separate environments
- Blue = Current production version
- Green = New version

Traffic switches instantly from blue to green.

---

# Flow

```text
Blue Environment → Current Live Version
Green Environment → New Version

Switch Traffic from Blue → Green
```

---

# Important Concepts from Image

## Maintain Two Environments

You maintain:

- Old environment
- New environment

Both run simultaneously.

---

# Traffic Switching

Traffic is switched instantly to new version.

---

# Rollback

If issues occur:

- Switch traffic back to old version immediately

Rollback is extremely fast.

---

# Advantages

- Zero downtime
- Very fast rollback
- Full testing before production switch
- Safer deployments

---

# Disadvantages

- Requires double infrastructure
- More expensive

---

# Best Use Cases

Use Blue-Green for:

- Critical production applications
- Banking systems
- E-commerce applications

---

# Blue-Green Deployment Diagram

```text
                +------------------+
                |     Service      |
                +------------------+
                   /            \
                  /              \
         +-------------+   +-------------+
         | Blue Env    |   | Green Env   |
         | Current App |   | New App     |
         +-------------+   +-------------+
```

---

# 4. Canary Deployment

## Concept

Canary deployment runs:

- Old version
- New version

simultaneously.

Traffic is divided between both versions.

---

# Traffic Distribution

Example:

- 80% traffic → Old version
- 20% traffic → New version

Gradually:

- Increase traffic to new version

---

# Important Concepts from Image

## Single Service

Both versions are accessed through same application endpoint.

---

# Gradual Traffic Shift

Initially:

```text
80% → Old Version
20% → New Version
```

Later:

```text
50% → Old Version
50% → New Version
```

Finally:

```text
100% → New Version
```

---

# Rollback

Rollback is easy.

Simply increase traffic back to old version.

---

# Advantages

- Lowest deployment risk
- Zero downtime
- Real production testing
- Fast rollback
- Controlled rollout

---

# Disadvantages

- More complex configuration
- Requires traffic management tools

---

# Canary Deployment Diagram

```text
                    +----------------------+
                    |    NGINX Ingress     |
                    +----------------------+
                         /            \
                        /              \
               80% Traffic          20% Traffic
                     /                    \
                    /                      \
        +------------------+     +----------------------+
        | Stable Version   |     | Canary Version       |
        +------------------+     +----------------------+
```

---

# Comparison Table
```
| Strategy | Downtime | Rollback | Risk | Infrastructure |
|---|---|---|---|---|
| Recreate | Yes | Slow | High | Low |
| Rolling Update | No | Medium | Medium | Low |
| Blue-Green | No | Fast | Low | High |
| Canary | No | Very Fast | Very Low | Medium |
```
---

# Which Strategy Should You Use?

## Recreate

Use for:
- Testing
- Development
- Non-critical apps

---

## Rolling Update

Use for:
- General applications
- Most Kubernetes workloads

---

## Blue-Green

Use for:
- Critical applications
- Applications requiring instant rollback

---

## Canary

Use for:
- Production systems
- Gradual feature rollout
- Safe deployments

---

# Summary

Kubernetes supports multiple deployment strategies.

Each strategy has different:

- Risk levels
- Downtime behavior
- Rollback speed
- Infrastructure requirements

Choosing the right deployment strategy depends on:

- Application criticality
- Downtime tolerance
- Infrastructure budget
- Deployment safety requirements


--------------------------------------------------------------------------

# Annotations Concept in Canary Deployment

## What are Annotations in Kubernetes?

Annotations are key-value metadata attached to Kubernetes objects.

They are mainly used to:

- Provide additional information
- Configure external tools/controllers
- Store non-identifying metadata

Unlike labels:

- Labels are used for selection/filtering
- Annotations are used for configuration and instructions

---

# Syntax of Annotations

Annotations are written inside:

```yaml
metadata:
  annotations:
```

Example:

```yaml
metadata:
  annotations:
    nginx.ingress.kubernetes.io/canary: "true"
```

---

# Why Annotations are Important in Canary Deployment?

In Canary deployment:

- NGINX Ingress Controller reads annotations
- Based on annotations, it decides:
  - How much traffic goes to canary version
  - Which ingress is stable
  - Which ingress is canary

Without annotations:

- NGINX treats ingress as normal ingress
- No traffic splitting happens

---

# Main Canary Annotations

## 1. Enable Canary Deployment

```yaml
nginx.ingress.kubernetes.io/canary: "true"
```

### Meaning

This tells NGINX:

"This ingress is a canary ingress."

---

# What Happens Internally?

Suppose we have:

- Stable ingress
- Canary ingress

When NGINX sees:

```yaml
nginx.ingress.kubernetes.io/canary: "true"
```

it understands:

- This ingress should receive only partial traffic
- Not full production traffic

---

# 2. Canary Weight Annotation

```yaml
nginx.ingress.kubernetes.io/canary-weight: "20"
```

### Meaning

Send:

- 20% traffic → Canary version
- 80% traffic → Stable version

---

# Traffic Flow Example

```text
100 Requests Coming to Application
        |
        |
   NGINX Ingress
      /     \
     /       \
80 Requests   20 Requests
to v1         to v2
```

---

# Complete Canary Ingress Example

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  # Canary ingress name
  name: ingress-canary

  annotations:

    # Enable canary deployment
    nginx.ingress.kubernetes.io/canary: "true"

    # Send 20% traffic to canary version
    nginx.ingress.kubernetes.io/canary-weight: "20"

spec:
  ingressClassName: nginx

  rules:
    - host: website01.example.com

      http:
        paths:
          - path: "/"
            pathType: Prefix

            backend:
              service:
                # Canary service
                name: mysvc-canary

                port:
                  number: 80
```

---

# Step-by-Step Working

## Step 1

User sends request:

```text
http://website01.example.com
```

---

## Step 2

NGINX ingress controller receives request.

---

## Step 3

NGINX checks annotations.

It sees:

```yaml
nginx.ingress.kubernetes.io/canary: "true"
```

So it knows:

- Canary routing is enabled

---

## Step 4

NGINX checks traffic percentage:

```yaml
nginx.ingress.kubernetes.io/canary-weight: "20"
```

Meaning:

- 20% requests → Canary service
- 80% requests → Stable service

---

# Real Example

Suppose 10 requests arrive.

Approximate routing:
```
| Request Number | Destination |
|---|---|
| 1 | Stable |
| 2 | Stable |
| 3 | Canary |
| 4 | Stable |
| 5 | Stable |
| 6 | Stable |
| 7 | Canary |
| 8 | Stable |
| 9 | Stable |
| 10 | Stable |
```
Approximately 20% goes to canary.

---

# Why Use Canary Weight?

It allows:

- Safe testing
- Gradual rollout
- Controlled production exposure

Instead of risking all users.

---

# Gradual Traffic Increase

Initially:

```yaml
nginx.ingress.kubernetes.io/canary-weight: "10"
```

Later:

```yaml
nginx.ingress.kubernetes.io/canary-weight: "30"
```

Then:

```yaml
nginx.ingress.kubernetes.io/canary-weight: "50"
```

Finally:

```yaml
nginx.ingress.kubernetes.io/canary-weight: "100"
```

---

# Rollback in Canary Deployment

If issues occur:

Simply:

```yaml
nginx.ingress.kubernetes.io/canary-weight: "0"
```

OR delete canary ingress.

Traffic immediately returns to stable version.

---

# Other Canary Annotations

NGINX also supports advanced canary annotations.

---

# Canary by Header

```yaml
nginx.ingress.kubernetes.io/canary-by-header: "X-Canary"
```

Only users with specific header go to canary version.

---

# Canary by Cookie

```yaml
nginx.ingress.kubernetes.io/canary-by-cookie: "canary"
```

Only users with specific cookie go to canary version.

---

# Difference Between Labels and Annotations
```
| Feature | Labels | Annotations |
|---|---|
| Purpose | Identification | Configuration |
| Used For | Selection/filtering | Metadata/instructions |
| Size Limit | Small | Can be larger |
| Example | app=nginx | canary-weight=20 |
```
---

# Important Concept

Annotations are not used by Kubernetes scheduler directly.

Instead:

- External controllers
- Operators
- Ingress controllers

read annotations and act accordingly.

In Canary deployment:

NGINX Ingress Controller reads canary annotations and performs traffic splitting.

---

# Summary

In Canary Deployment:

Annotations control:

- Whether canary routing is enabled
- How much traffic goes to canary version
- Advanced routing behavior

Main annotations:

```yaml
# Enable canary deployment
nginx.ingress.kubernetes.io/canary: "true"
```

```yaml
# Send 20% traffic to canary version
nginx.ingress.kubernetes.io/canary-weight: "20"
```



--------------------------------------------------------------------------




# Canary Deployment in Kubernetes

## What is Canary Deployment?

Canary Deployment is a deployment strategy where:

- Two versions of an application run simultaneously
- Small percentage of traffic goes to the new version first
- Remaining traffic continues going to stable version

This helps test the new version safely before sending all traffic to it.

---

# Canary Deployment Architecture Diagram

```text
                    +----------------------+
                    |    NGINX Ingress     |
                    +----------------------+
                         /            \
                        /              \
               80% Traffic          20% Traffic
                     /                    \
                    /                      \
        +------------------+     +----------------------+
        | Stable Ingress   |     | Canary Ingress       |
        +------------------+     +----------------------+
                 |                          |
                 |                          |
        +------------------+     +----------------------+
        | Service v1       |     | Service v2 Canary    |
        +------------------+     +----------------------+
                 |                          |
                 |                          |
        +------------------+     +----------------------+
        | Deployment v1    |     | Deployment v2        |
        | app = stable     |     | app = canary         |
        +------------------+     +----------------------+
```

---

# Stable Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: app-deploy-v1-stable

spec:
  # Number of pods
  replicas: 3

  selector:
    matchLabels:
      # Application label
      app: kubeserve

      # Stable version label
      version: stable

  template:
    metadata:
      labels:
        # Application label
        app: kubeserve

        # Stable version label
        version: stable

    spec:
      containers:
        - name: c1

          # Stable application image
          image: leaddevops/kubeserve:v1
```

---

# Stable Service YAML

```yaml
apiVersion: v1
kind: Service

metadata:
  # Service name
  name: mysvc

spec:
  # NodePort service type
  type: NodePort

  selector:
    # Select stable pods only
    app: kubeserve
    version: stable

  ports:
    - targetPort: 80

      # Service port
      port: 80
```

---

# Canary Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Canary deployment name
  name: app-deploy-v2-canary

spec:
  # Number of pods
  replicas: 3

  selector:
    matchLabels:
      # Application label
      app: kubeserve

      # Canary version label
      version: canary

  template:
    metadata:
      labels:
        # Application label
        app: kubeserve

        # Canary version label
        version: canary

    spec:
      containers:
        - name: c1

          # Canary image
          image: leaddevops/kubeserve:v2
```

---

# Canary Service YAML

```yaml
apiVersion: v1
kind: Service

metadata:
  # Canary service name
  name: mysvc-canary

spec:
  # NodePort service
  type: NodePort

  selector:
    # Select canary pods only
    app: kubeserve
    version: canary

  ports:
    - targetPort: 80

      # Service port
      port: 80
```

---

# Stable Ingress YAML

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  # Stable ingress name
  name: ingress-stable

spec:
  # Use nginx ingress controller
  ingressClassName: nginx

  rules:
    - host: website01.example.com

      http:
        paths:
          - pathType: Prefix

            # Route all traffic
            path: "/"

            backend:
              service:
                # Stable service
                name: mysvc

                port:
                  number: 80
```

---

# Canary Ingress YAML

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  # Canary ingress name
  name: ingress-canary

  annotations:

    # Enable canary deployment
    nginx.ingress.kubernetes.io/canary: "true"

    # Send 20 percent traffic to canary
    nginx.ingress.kubernetes.io/canary-weight: "20"

spec:
  # Use nginx ingress controller
  ingressClassName: nginx

  rules:
    - host: website01.example.com

      http:
        paths:
          - pathType: Prefix

            # Route all traffic
            path: "/"

            backend:
              service:
                # Canary service
                name: mysvc-canary

                port:
                  number: 80
```

---

# Apply All YAML Files

```bash
kubectl apply -f deployment-stable.yml
kubectl apply -f service-stable.yml
kubectl apply -f deployment-canary.yml
kubectl apply -f service-canary.yml
kubectl apply -f ingress-stable.yml
kubectl apply -f ingress-canary.yml
```

---

# Configure Hosts File

```bash
# Become root user
sudo su -

# Edit hosts file
vim /etc/hosts
```

Add:

```text
35.238.236.214 website01.example.com
```

---

# Test Traffic Distribution

```bash
while true; do
  curl http://website01.example.com/
  sleep 1
done
```

---

# Expected Output

You will observe:

- 80 percent traffic goes to v1
- 20 percent traffic goes to v2

because of:

```yaml
nginx.ingress.kubernetes.io/canary-weight: "20"
```

---

# Advantages of Canary Deployment

- Zero downtime
- Low deployment risk
- Easy rollback
- Real production testing
- Gradual traffic shifting

---

# Rollback

If v2 fails:

- Remove canary ingress
OR
- Set canary weight to 0

Traffic immediately goes back to stable version.


# Kubernetes Persistent Volumes (PV) and Persistent Volume Claims (PVC)

## What is a Persistent Volume (PV)?

A Persistent Volume (PV) is a storage resource provided by a Kubernetes administrator inside the cluster.

Think of a PV as a storage disk that Kubernetes Pods can use.

A PV contains information such as:

- Storage type
- Storage capacity
- Access mode
- Status
- StorageClass

---

## Information Stored in a PV

### Storage Type

Examples:

- HostPath
- NFS
- AWS EBS
- Azure Disk

### Capacity

```yaml
capacity:
  storage: 1Gi
```

Means 1Gi storage is available.

### Access Modes

```yaml
accessModes:
  - ReadWriteOnce
```

### Status

Possible states:

- Available
- Bound
- Released
- Failed

### StorageClass

```yaml
storageClassName: manual
```

Indicates whether storage was created manually or dynamically.

---

# What is a Persistent Volume Claim (PVC)?

PVC is a request for storage.

Think of PVC as:

> A request raised by an administrator or developer to reserve storage.

PVC specifies:

- Required volume size
- Access mode
- Storage class

Example:

```yaml
resources:
  requests:
    storage: 1Gi
```

---

# PV and PVC Relationship

```text
Administrator Creates PV
           ↓
Developer Creates PVC
           ↓
PVC Binds To PV
           ↓
Pod Uses PVC
```

---

# Demo 1: HostPath Volume

## What is HostPath?

HostPath creates storage directly on the worker node filesystem.

Example:

```text
Worker Node
   |
   +-- /tmp/data
```

The storage physically exists on the node where Pod runs.

---

# Use Cases

Use HostPath when:

- Storage is internal to cluster
- Learning Kubernetes
- Testing environments
- Single node clusters

Not recommended for production.

---

# Step 1: Create Persistent Volume

```bash
# Create PV YAML
vim pv.yml
```

## PV YAML

```yaml
apiVersion: v1
kind: PersistentVolume

metadata:
  # Persistent Volume name
  name: block-pv

spec:

  # Storage class name
  storageClassName: manual

  capacity:
    # Total storage available
    storage: 1Gi

  accessModes:
    # One node can read and write
    - ReadWriteOnce

  hostPath:
    # Physical directory on worker node
    path: /tmp/data
```

---

# Create PV

```bash
# Create Persistent Volume
kubectl create -f pv.yml
```

---

# Verify PV

```bash
# View Persistent Volumes
kubectl get pv
```

Example:

```text
NAME       CAPACITY   STATUS
block-pv   1Gi        Available
```

---

# Step 2: Create Persistent Volume Claim

```bash
# Create PVC YAML
vim pvc.yml
```

## PVC YAML

```yaml
apiVersion: v1
kind: PersistentVolumeClaim

metadata:
  # PVC name
  name: pvc

spec:

  resources:
    requests:
      # Request 1Gi storage
      storage: 1Gi

  # Match PV storage class
  storageClassName: manual

  accessModes:
    # Must match PV access mode
    - ReadWriteOnce
```

---

# Create PVC

```bash
# Create PVC
kubectl create -f pvc.yml
```

---

# Verify PVC

```bash
# Check PVC
kubectl get pvc
```

Example:

```text
NAME   STATUS   VOLUME
pvc    Bound    block-pv
```

---

# What Does Bound Mean?

```text
PV Available
      ↓
PVC Created
      ↓
PV Reserved
      ↓
Status = Bound
```

The PV is now reserved for this PVC.

---

# Step 3: Create Pod Using PVC

```bash
# Create Pod YAML
vim pod-pvc.yml
```

## Pod YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  # Pod name
  name: pod-pvc

spec:

  containers:

    - name: c1

      # NGINX container image
      image: nginx

      volumeMounts:

        # Mount volume inside container
        - mountPath: "/data"

          # Volume name reference
          name: my-volume

  volumes:

    - name: my-volume

      persistentVolumeClaim:

        # PVC used by pod
        claimName: pvc
```

---

# Explanation

## volumeMounts

```yaml
volumeMounts:
  - mountPath: "/data"
```

Mounts storage inside container.

Container sees storage at:

```text
/data
```

---

## persistentVolumeClaim

```yaml
persistentVolumeClaim:
  claimName: pvc
```

Tells Kubernetes:

Use storage reserved by PVC.

---

# Create Pod

```bash
# Create Pod
kubectl create -f pod-pvc.yml
```

---

# Verify Pod

```bash
# View Pod and node information
kubectl get pods -o wide
```

Example:

```text
NAME      NODE
pod-pvc   worker-node
```

---

# Storage Flow Diagram

```text
Worker Node
│
├── /tmp/data
│
└── Persistent Volume (PV)
          │
          ▼
Persistent Volume Claim (PVC)
          │
          ▼
Pod
          │
          ▼
Container Path (/data)
```

---

# Verify Storage on Worker Node

Login to worker node where pod is running.

Check:

```bash
# Verify storage directory
ls -ld /tmp/data
```

You should see:

```text
/tmp/data
```

directory created on the node.

---

# Data Persistence Example

Enter Pod:

```bash
# Open shell inside pod
kubectl exec -it pod-pvc -- bash
```

Create file:

```bash
echo "Hello PV" > /data/test.txt
```

Delete Pod:

```bash
kubectl delete pod pod-pvc
```

Create Pod again.

The file still exists because data is stored in PV.

---

# PV Lifecycle

```text
Available
   ↓
Bound
   ↓
In Use
   ↓
Released
```

---

# Real World Flow

```text
Administrator Creates PV
            ↓
Developer Creates PVC
            ↓
PVC Binds To PV
            ↓
Pod Uses PVC
            ↓
Application Stores Data
```

---

# Summary

## Persistent Volume (PV)

- Actual storage resource
- Created by administrator
- Provides storage

## Persistent Volume Claim (PVC)

- Requests storage
- Reserves storage
- Binds to PV

## Pod

- Uses PVC
- Mounts storage inside container

---

# Final Architecture

```text
Persistent Volume (PV)
        │
        ▼
Persistent Volume Claim (PVC)
        │
        ▼
Pod
        │
        ▼
Container Path (/data)
        │
        ▼
Physical Storage (/tmp/data)
```


# Dynamic Provisioning of Volumes in Kubernetes

# What is Dynamic Provisioning?

In traditional Persistent Volumes:

- Administrator manually creates PV
- User creates PVC
- PVC binds to PV

This is called **Static Provisioning**.

In Dynamic Provisioning:

- No need to manually create PV
- Kubernetes automatically creates storage
- Kubernetes automatically creates PV
- PVC automatically binds to the PV

This process is handled using a **StorageClass**.

---

# Role of CSI (Container Storage Interface)

Kubernetes includes a plugin framework called:

```text
CSI (Container Storage Interface)
```

CSI is responsible for:

- Connecting Kubernetes to cloud storage providers
- Creating storage automatically
- Managing storage lifecycle
- Attaching storage to nodes

Examples:

- Google Persistent Disk
- AWS EBS
- Azure Disk
- NetApp
- Ceph

---

# Dynamic Provisioning Flow

## Step 1

User creates a PVC.

```text
Need 10Gi SSD Storage
```

---

## Step 2

PVC references a StorageClass.

Example:

```yaml
storageClassName: fast
```

---

## Step 3

CSI Driver contacts cloud provider.

Example:

```text
Google Cloud Platform
```

---

## Step 4

Cloud creates storage.

Example:

```text
10Gi SSD Persistent Disk
```

---

## Step 5

Kubernetes automatically creates a PV.

---

## Step 6

PV binds to PVC.

---

## Step 7

Pod uses PVC.

---

# Diagram Based on Image

```text
                  +---------------------+
                  |    Kubernetes       |
                  |      Cluster        |
                  +---------------------+
                            |
                            |
                            ▼
                  +---------------------+
                  |   StorageClass      |
                  |      fast           |
                  +---------------------+
                            |
                            |
                            ▼
                 +-----------------------+
                 |   CSI Driver          |
                 | Container Storage     |
                 | Interface             |
                 +-----------------------+
                            |
                            |
                            ▼
        +-----------------------------------------+
        | Google Cloud Platform                   |
        | Persistent Disk Storage                 |
        |                                         |
        |         10Gi SSD Disk                   |
        +-----------------------------------------+
                            |
                            |
                            ▼
                 +----------------------+
                 | Persistent Volume    |
                 |      (PV)            |
                 +----------------------+
                            |
                            |
                            ▼
                 +----------------------+
                 | Persistent Volume    |
                 | Claim (PVC)          |
                 +----------------------+
                            |
                            |
                            ▼
                 +----------------------+
                 |        Pod           |
                 +----------------------+
                            |
                            |
                            ▼
                 +----------------------+
                 | Mounted Storage      |
                 |      /data           |
                 +----------------------+
```
![DynamicProvision](images/Dynamic_Provision.jpg)

![PVC](images/pvc.jpg)
---

# Why StorageClass is Required?

StorageClass tells Kubernetes:

- Which storage provider to use
- What type of disk to create
- Which CSI driver to use

Without StorageClass:

- Dynamic provisioning cannot happen

---

# StorageClass YAML

Create file:

```bash
# Create StorageClass YAML
vim sc.yml
```

## StorageClass Definition

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass

metadata:
  # StorageClass name
  name: fast

# Provisioner used to create storage
provisioner: kubernetes.io/gce-pd

parameters:

  # Create SSD based disk
  type: pd-ssd
```

---

# Explanation

## StorageClass Name

```yaml
name: fast
```

Used by PVC.

---

## Provisioner

```yaml
provisioner: kubernetes.io/gce-pd
```

Tells Kubernetes:

Use Google Persistent Disk service.

---

## Parameters

```yaml
parameters:
  type: pd-ssd
```

Create SSD disk.

---

# Create PVC

Create file:

```bash
# Create PVC YAML
vim pvc-pd.yml
```

## PVC YAML

```yaml
apiVersion: v1
kind: PersistentVolumeClaim

metadata:
  # PVC name
  name: sc-pvc

spec:

  # StorageClass to use
  storageClassName: fast

  resources:
    requests:

      # Request 10Gi storage
      storage: 10Gi

  accessModes:

    # One node can read/write
    - ReadWriteOnce
```

---

# What Happens When PVC is Created?

Kubernetes performs:

```text
PVC Created
      ↓
StorageClass Found
      ↓
CSI Driver Called
      ↓
10Gi SSD Disk Created
      ↓
PV Automatically Created
      ↓
PVC Bound To PV
```

No manual PV creation is required.

---

# Verify Resources

```bash
# Check PVC
kubectl get pvc

# Check automatically created PV
kubectl get pv
```

Example:

```text
NAME     STATUS   VOLUME
sc-pvc   Bound    pvc-123abc
```

---

# Create Pod Using PVC

Create file:

```bash
# Create Pod YAML
vim pod-pvc.yml
```

## Pod YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  # Pod name
  name: pod-pvc

spec:
  containers:

    - image: nginx

      # Container name
      name: c1

      volumeMounts:

        # Mount storage inside container
        - mountPath: "/data"

          # Volume reference
          name: my-volume

  volumes:

    - name: my-volume

      persistentVolumeClaim:

        # PVC used by Pod
        claimName: sc-pvc
```

---

# Storage Flow Inside Pod

```text
Google SSD Disk
       ↓
Persistent Volume
       ↓
Persistent Volume Claim
       ↓
Pod
       ↓
/data
```

---

# Benefits of Dynamic Provisioning

## No Manual PV Creation

Administrator does not need to create PVs manually.

---

## Faster Provisioning

Storage is created automatically when needed.

---

## Cloud Integration

Works with:

- GCP
- AWS
- Azure

---

## Better Scalability

Suitable for production clusters.

---

# Static vs Dynamic Provisioning
```
| Feature | Static Provisioning | Dynamic Provisioning |
|----------|----------|----------|
| PV Creation | Manual | Automatic |
| PVC Creation | Manual | Manual |
| Storage Creation | Manual | Automatic |
| Cloud Integration | Limited | Excellent |
| Administration Effort | High | Low |
```
---

# Complete Architecture

```text
StorageClass
      ↓
CSI Driver
      ↓
Cloud Storage (10Gi SSD)
      ↓
Persistent Volume (Auto Created)
      ↓
Persistent Volume Claim
      ↓
Pod
      ↓
Container Path (/data)
```

---

# Summary

Dynamic Provisioning allows Kubernetes to:

- Automatically create cloud storage
- Automatically create PVs
- Bind PV to PVC
- Mount storage into Pods

Key Components:

1. StorageClass
2. CSI Driver
3. Cloud Storage
4. Persistent Volume
5. Persistent Volume Claim
6. Pod

This is the preferred storage approach in production Kubernetes clusters.



# Kubernetes ConfigMaps and Secrets

## ConfigMaps

### What is a ConfigMap?

A ConfigMap is a Kubernetes API object used to store non-confidential configuration data in key-value format.

ConfigMaps help separate application configuration from container images.

Benefits:

- Same image can be used in multiple environments
- No image rebuild required for config changes
- Easy application portability

---

## ConfigMap Architecture

```text
Properties File
      ↓
ConfigMap
      ↓
Pod
      ↓
Mounted as File / Environment Variable
```

---

## Create Development Properties File

```bash
# Create development properties file
vim dev.properties
```

```properties
app.env=dev
app.mem=2048
app.url=dev.com
```

---

## Create Production Properties File

```bash
# Create production properties file
vim prod.properties
```

```properties
app.env=prod
app.mem=4048
app.url=prod.com
```

---

## Create ConfigMaps

```bash
# Create dev ConfigMap
kubectl create configmap dev-config --from-file=dev.properties

# Create prod ConfigMap
kubectl create configmap prod-config --from-file=prod.properties
```

---

## Dev Pod YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  # Pod name
  name: dev-pod

spec:
  containers:
    - name: c1

      # NGINX image
      image: nginx

      volumeMounts:

        # Mount ConfigMap
        - name: config-volume

          # Directory inside container
          mountPath: /etc/config

  volumes:
    - name: config-volume

      configMap:

        # ConfigMap name
        name: dev-config
```

---

## Prod Pod YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  # Pod name
  name: prod-pod

spec:
  containers:
    - name: c1

      # NGINX image
      image: nginx

      volumeMounts:

        # Mount ConfigMap
        - name: config-volume

          # Directory inside container
          mountPath: /etc/config

  volumes:
    - name: config-volume

      configMap:

        # ConfigMap name
        name: prod-config
```

---

## Verify ConfigMap Inside Pod

```bash
# Login to pod
kubectl exec -it dev-pod -- bash

# Go to mounted directory
cd /etc/config

# View files
ls
```

---

## Update Existing ConfigMap

```bash
# Edit ConfigMap
kubectl edit configmap dev-config -o yaml
```

After saving changes:

```bash
kubectl exec -it dev-pod -- bash
cd /etc/config
cat dev.properties
```

Updated values will be visible.

---

# Secrets

## What is a Secret?

A Secret stores sensitive information such as:

- Passwords
- Tokens
- API Keys
- Certificates

Secrets are similar to ConfigMaps but intended for confidential data.

---

## Why Use Secrets?

Avoid storing passwords inside:

- Source code
- Container images
- Deployment YAML

Store them in Secrets instead.

---

## ConfigMap vs Secret
```
| Feature | ConfigMap | Secret |
|----------|------------|---------|
| Purpose | Configuration | Sensitive Data |
| Example | URLs, App Settings | Passwords, Tokens |
| Security | Not Secure | More Secure |
```
---

## Secret YAML

```yaml
apiVersion: v1
kind: Secret

metadata:
  # Secret name
  name: mysql-pwd

data:

  # Base64 encoded password
  password: "cGFzc3dvcmQ="
```

---

## Decode Secret Value

```bash
echo cGFzc3dvcmQ= | base64 -d
```

Output:

```text
password
```

---

## Create Secret

```bash
# Create Secret
kubectl create -f secrets.yml
```

---

## Verify Secret

```bash
# View Secrets
kubectl get secret

# Describe Secret
kubectl describe secret mysql-pwd
```

---

## Secret Architecture

```text
Password
    ↓
Base64 Encoding
    ↓
Secret
    ↓
Pod
    ↓
Application
```

---

# Summary

## ConfigMap

Used for:

- URLs
- Environment settings
- Application configuration

Not suitable for sensitive data.

## Secret

Used for:

- Passwords
- Tokens
- API Keys

Recommended for confidential information.


# Kubernetes Mini Project , Deployment + Service + ConfigMap + Secret , WordPress + MySQL Application

---

## Project Overview

In this project we deploy:

1. MySQL Database
2. WordPress Application
3. ConfigMap
4. Secret
5. ClusterIP Service
6. NodePort Service

The goal is to understand how ConfigMaps and Secrets are used inside Deployments.

---

## Real World Scenario

Suppose we have a WordPress application.

WordPress needs:

- Database Host
- Database Username
- Database Password
- Database Name

Instead of hardcoding values:

- Store non-sensitive data in ConfigMap
- Store sensitive data in Secret

This follows Kubernetes best practices.

---

## Architecture Diagram

```text
                    +-------------------------+
                    |        Browser          |
                    +------------+------------+
                                 |
                                 |
                                 v
                    +-------------------------+
                    |    WordPress Service    |
                    |       NodePort          |
                    +------------+------------+
                                 |
                                 |
                                 v
                    +-------------------------+
                    | WordPress Deployment    |
                    +-------------------------+
                    | DB Host -> ConfigMap    |
                    | DB User -> ConfigMap    |
                    | DB Pass -> Secret       |
                    +------------+------------+
                                 |
                                 |
                                 v
                    +-------------------------+
                    |   MySQL Service         |
                    |      ClusterIP          |
                    +------------+------------+
                                 |
                                 |
                                 v
                    +-------------------------+
                    |   MySQL Deployment      |
                    +-------------------------+
                    | DB Name -> ConfigMap    |
                    | RootPwd -> Secret       |
                    +-------------------------+
```

---

## How Data Flows

```text
ConfigMap
   |
   +---- MYSQL_DATABASE
   +---- WORDPRESS_DB_HOST
   +---- WORDPRESS_DB_USER
   |
   v

Secret
   |
   +---- MYSQL PASSWORD
   |
   v

MySQL Deployment
   |
   v

WordPress Deployment
   |
   v

Browser Access
```

---

## Step 1: Create Secret

Secret stores sensitive data.

Example:

- Database password
- API keys
- Tokens

Create file:

```bash
# Create secret YAML
vim secrets.yml
```

---

### Secret YAML

```yaml
apiVersion: v1
kind: Secret

metadata:
  # Secret name
  name: mysql-pwd

data:

  # Base64 encoded password
  password: "cGFzc3dvcmQ="
```

---

### Decode Password

```bash
echo cGFzc3dvcmQ= | base64 -d
```

Output:

```text
password
```

---

### Create Secret

```bash
# Create secret
kubectl create -f secrets.yml
```

---

### Verify Secret

```bash
# View secrets
kubectl get secrets

# Describe secret
kubectl describe secret mysql-pwd
```

---

## Step 2: Create ConfigMap

ConfigMap stores:

- Database name
- Database host
- Database username

Create file:

```bash
# Create ConfigMap YAML
vim config-mysql.yml
```

---

## ConfigMap YAML

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  # ConfigMap name
  name: mysql-config

data:

  # Database name
  MYSQL_DATABASE: wordpress

  # Database username
  WORDPRESS_DB_USER: root

  # Database host
  WORDPRESS_DB_HOST: mysql
```

---

### Create ConfigMap

```bash
# Create ConfigMap
kubectl create -f config-mysql.yml
```

---

### Verify ConfigMap

```bash
kubectl get configmap

kubectl describe configmap mysql-config
```

---

## Step 3: Deploy MySQL

Create file:

```bash
# Create MySQL deployment YAML
vim deploy-mysql.yml
```

---

### MySQL Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: mysql

spec:

  # Number of replicas
  replicas: 1

  selector:
    matchLabels:
      app: mysql-wordpress

  template:
    metadata:
      labels:
        app: mysql-wordpress
        product: mysql

    spec:
      containers:

        - name: mysql-container

          # MySQL image
          image: mysql

          env:

            # Root password from Secret
            - name: MYSQL_ROOT_PASSWORD

              valueFrom:
                secretKeyRef:

                  # Secret name
                  name: mysql-pwd

                  # Secret key
                  key: password

            # Database name from ConfigMap
            - name: MYSQL_DATABASE

              valueFrom:
                configMapKeyRef:

                  # ConfigMap name
                  name: mysql-config

                  # ConfigMap key
                  key: MYSQL_DATABASE

---

apiVersion: v1
kind: Service

metadata:
  # MySQL service name
  name: mysql

spec:

  # Internal service
  type: ClusterIP

  ports:

    - targetPort: 3306

      # Service port
      port: 3306

  selector:
    app: mysql-wordpress
    product: mysql
```

---

### Create MySQL Deployment

```bash
kubectl create -f deploy-mysql.yml
```

---

### MySQL Environment Variables

MySQL receives:

```text
MYSQL_ROOT_PASSWORD -> Secret
MYSQL_DATABASE      -> ConfigMap
```

---

## Step 4: Deploy WordPress

Create file:

```bash
# Create WordPress deployment YAML
vim deploy-wordpress.yml
```

---

### WordPress Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: wordpress

spec:

  # Number of replicas
  replicas: 1

  selector:
    matchLabels:
      app: mysql-wordpress
      tier: frontend

  template:
    metadata:
      labels:
        app: mysql-wordpress
        tier: frontend

    spec:
      containers:

        - name: wordpress-container

          # WordPress image
          image: wordpress

          env:

            # Database host from ConfigMap
            - name: WORDPRESS_DB_HOST

              valueFrom:
                configMapKeyRef:
                  name: mysql-config
                  key: WORDPRESS_DB_HOST

            # Database user from ConfigMap
            - name: WORDPRESS_DB_USER

              valueFrom:
                configMapKeyRef:
                  name: mysql-config
                  key: WORDPRESS_DB_USER

            # Database password from Secret
            - name: WORDPRESS_DB_PASSWORD

              valueFrom:
                secretKeyRef:
                  name: mysql-pwd
                  key: password

---

apiVersion: v1
kind: Service

metadata:
  # WordPress service name
  name: wordpress

spec:

  # Expose application externally
  type: NodePort

  ports:

    - targetPort: 80

      # Service port
      port: 80

  selector:
    app: mysql-wordpress
    tier: frontend
```

---

### Create WordPress Deployment

```bash
kubectl create -f deploy-wordpress.yml
```

---

### WordPress Environment Variables

WordPress receives:

```text
WORDPRESS_DB_HOST     -> ConfigMap
WORDPRESS_DB_USER     -> ConfigMap
WORDPRESS_DB_PASSWORD -> Secret
```

---

## Verify Resources

```bash
kubectl get all
```

Expected Output:

```text
MySQL Pod

MySQL Service

WordPress Pod

WordPress Service
```

---

## Check Pods

```bash
kubectl get pods
```

---

## Check Services

```bash
kubectl get svc
```

Example:

```text
NAME        TYPE        PORT(S)
mysql       ClusterIP   3306/TCP
wordpress   NodePort    80:32080/TCP
```

---

## Access WordPress

Open browser:

```text
http://<Node-IP>:<NodePort>
```

Example:

```text
http://192.168.1.10:32080
```

---

## Verify Environment Variables

MySQL:

```bash
kubectl exec -it <mysql-pod> -- env
```

WordPress:

```bash
kubectl exec -it <wordpress-pod> -- env
```

---

## Interview Question

Q. Why not store password in ConfigMap?

Answer:

ConfigMap is not secure.

Secrets should be used for:

- Passwords
- Tokens
- API Keys

---

## ConfigMap vs Secret
```
| Feature | ConfigMap | Secret |
|----------|------------|---------|
| Purpose | Configuration | Sensitive Data |
| Security | No | Better |
| Data Format | Plain Text | Base64 Encoded |
| Example | URLs, Hostnames | Passwords, Tokens |
```
---

## Troubleshooting

### MySQL Pod CrashLoopBackOff

Check:

```bash
kubectl logs <mysql-pod>
```

Possible reason:

Wrong password format.

---

## WordPress Cannot Connect To DB

Check:

```bash
kubectl describe pod <wordpress-pod>
```

Verify:

```text
WORDPRESS_DB_HOST
WORDPRESS_DB_USER
WORDPRESS_DB_PASSWORD
```

---

## Service Not Accessible

Check:

```bash
kubectl get svc
```

Verify NodePort assigned.

---

## Complete Flow

```text
Secret
   |
   +---- Database Password
   |
   v

ConfigMap
   |
   +---- Database Name
   +---- Database Host
   +---- Database User
   |
   v

MySQL Deployment
   |
   +---- ClusterIP Service
   |
   v

WordPress Deployment
   |
   +---- NodePort Service
   |
   v

Browser
```

---

## Summary

This project demonstrates:

- Secret Creation
- ConfigMap Creation
- MySQL Deployment
- MySQL ClusterIP Service
- WordPress Deployment
- WordPress NodePort Service
- Environment Variables from ConfigMap
- Environment Variables from Secret

This is one of the most common Kubernetes interview and production-level examples.


# Kubernetes Mini Project
# NFS Storage + PV + PVC + Deployments + Services + Secrets + ConfigMaps

## Architecture Diagram (Based on Provided Image)

![Cluster](images/kuberenets-cluster.jpg)


```text
+------------------------------------------------------------------+
|                    KUBERNETES CLUSTER                            |
|                                                                  |
|  MASTER NODE                                 WORKER NODE         |
|  -----------                                 -----------         |
|                                                                  |
|  [1] NFS Server (/data)  ----------------->  NFS Client          |
|                                                                  |
|  [2] NFS Persistent Volume                                       |
|          |                                                       |
|          v                                                       |
|  [4] PVC (NFS)                                                   |
|          |                                                       |
|          v                                                       |
|  [6] MySQL Deployment --------------------> /var/lib/mysql       |
|          |                                                       |
|  [7] MySQL ClusterIP Service                                     |
|                                                                  |
|  [5] Secret                                                      |
|          |                                                       |
|          +---- MYSQL Password                                    |
|                                                                  |
|  [11] HostPath PV                                                |
|          |                                                       |
|  [12] WordPress Deployment -------------> /var/www/html          |
|          |                                                       |
|          +---- Uses ConfigMap + Secret                           |
|          |                                                       |
|  [13] WordPress NodePort Service                                 |
|                                                                  |
|  [16] Browser Access ---> NodePort ---> WordPress                |
+------------------------------------------------------------------+
```

---

# Project Objective

Deploy:

- NFS Server
- NFS Client
- Persistent Volume
- Persistent Volume Claim
- MySQL Deployment
- MySQL Service
- WordPress Deployment
- WordPress Service
- ConfigMap
- Secret

---

# Step 1: Configure NFS Server

Create shared directory:

```bash
# Become root user
sudo su

# Create shared directory
mkdir -p /data

# Verify directory
ls -alrt /data
```

---

# Install NFS Server

```bash
# Install NFS server
sudo apt update

sudo apt install nfs-kernel-server -y
```

---

# Configure Permissions

```bash
# Change ownership
sudo chown nobody:nogroup /data

# Give full permissions
sudo chmod 777 /data
```

---

# Configure NFS Export

Edit exports file:

```bash
sudo vi /etc/exports
```

Add:

```text
/data *(rw,sync,no_root_squash)
```

Explanation:

- rw = Read Write
- sync = Write changes immediately
- no_root_squash = Allow root access from clients

---

# Apply Export Configuration

```bash
# Export shared directories
sudo exportfs -rv
```

---

# Restart NFS Server

```bash
sudo systemctl restart nfs-kernel-server
```

---

# Step 2: Install NFS Client on Worker Node

```bash
# Install NFS client package
sudo apt install nfs-common -y
```

---

# NFS Flow

```text
NFS Server
    |
    +---- /data
    |
    v
Worker Node
    |
    v
Pods Access Shared Storage
```

---

# Step 3: Create Secret

```yaml
apiVersion: v1
kind: Secret

metadata:
  # Secret name
  name: mysql-secret

data:
  # Base64 encoded password
  password: cGFzc3dvcmQ=
```

---

# Step 4: Create ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap

metadata:
  # ConfigMap name
  name: mysql-config

data:
  # Database name
  MYSQL_DATABASE: wordpress

  # Database host
  WORDPRESS_DB_HOST: mysql

  # Database user
  WORDPRESS_DB_USER: root
```

---

# Step 5: NFS Persistent Volume

```yaml
apiVersion: v1
kind: PersistentVolume

metadata:
  # PV name
  name: mysql-nfs-pv

spec:

  # Storage capacity
  capacity:
    storage: 5Gi

  # Access mode
  accessModes:
    - ReadWriteMany

  nfs:

    # NFS shared folder
    path: /data

    # NFS server IP
    server: 172.31.58.69
```

---

# Explanation

ReadWriteMany means:

Multiple pods can access storage simultaneously.

---

# Step 6: NFS PVC

```yaml
apiVersion: v1
kind: PersistentVolumeClaim

metadata:
  # PVC name
  name: mysql-nfs-pvc

spec:

  accessModes:
    - ReadWriteMany

  resources:
    requests:

      # Requested storage
      storage: 5Gi
```

---

# PV-PVC Binding

```text
Persistent Volume
        |
        v
Persistent Volume Claim
        |
        v
MySQL Pod
```

---

# Step 7: MySQL Deployment

MySQL stores database files inside NFS volume.

```text
MySQL
   |
   v
/var/lib/mysql
   |
   v
NFS Storage
```

Environment variables:

```text
MYSQL_ROOT_PASSWORD -> Secret

MYSQL_DATABASE -> ConfigMap
```

---

# MySQL Service

```yaml
apiVersion: v1
kind: Service

metadata:
  # Service name
  name: mysql

spec:

  # Internal service
  type: ClusterIP

  ports:
    - port: 3306
      targetPort: 3306

  selector:
    app: mysql
```

---

# Why ClusterIP?

Because:

- Only WordPress needs MySQL access.
- No external users should access MySQL.

---

# Step 8: WordPress HostPath Storage

HostPath stores WordPress content.

Example:

```text
/opt/wordpress
```

on worker node.

---

# HostPath PV

```yaml
apiVersion: v1
kind: PersistentVolume

metadata:
  # PV name
  name: wordpress-hostpath-pv

spec:

  capacity:
    storage: 5Gi

  accessModes:
    - ReadWriteOnce

  hostPath:

    # Worker node directory
    path: /opt/wordpress
```

---

# HostPath PVC

```yaml
apiVersion: v1
kind: PersistentVolumeClaim

metadata:
  # PVC name
  name: wordpress-pvc

spec:

  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 5Gi
```

---

# Step 9: WordPress Deployment

WordPress uses:

ConfigMap:

```text
WORDPRESS_DB_HOST
WORDPRESS_DB_USER
```

Secret:

```text
WORDPRESS_DB_PASSWORD
```

PVC:

```text
/var/www/html
```

---

# WordPress Service

```yaml
apiVersion: v1
kind: Service

metadata:
  # WordPress service
  name: wordpress

spec:

  # External access
  type: NodePort

  ports:
    - port: 80
      targetPort: 80

  selector:
    app: wordpress
```

---

# Why NodePort?

Allows browser access.

```text
Browser
   |
   v
NodeIP:NodePort
   |
   v
WordPress
```

---

# Verification Commands

```bash
# View all resources
kubectl get all

# View PVs
kubectl get pv

# View PVCs
kubectl get pvc

# View Pods
kubectl get pods

# View Services
kubectl get svc
```

---

# Complete Project Flow

```text
NFS Server
    |
    v
NFS PV
    |
    v
PVC
    |
    v
MySQL Deployment
    |
    +---- Secret (Password)
    |
    +---- ConfigMap (DB Name)
    |
    v
MySQL Service (ClusterIP)
    |
    v
WordPress Deployment
    |
    +---- Secret (Password)
    |
    +---- ConfigMap (Host/User)
    |
    +---- HostPath Storage
    |
    v
WordPress Service (NodePort)
    |
    v
Browser Access
```

---

# Interview Questions

## Why use NFS?

To share storage across multiple nodes.

---

## Why use PVC?

To decouple Pods from storage.

---

## Why use Secret?

To store passwords securely.

---

## Why use ConfigMap?

To store application configuration.

---

## Why ClusterIP for MySQL?

Internal communication only.

---

## Why NodePort for WordPress?

External browser access.

---

# Summary

This project combines:

- NFS Storage
- Persistent Volumes
- Persistent Volume Claims
- ConfigMaps
- Secrets
- MySQL Deployment
- WordPress Deployment
- ClusterIP Service
- NodePort Service

It is one of the most common end-to-end Kubernetes storage projects used in training and interviews.




# Kubernetes Scheduling - Detailed Notes

## What is Scheduling?

Scheduling is the process of deciding which node should run a Pod.

When a Pod is created:

1. API Server receives request
2. Scheduler finds a suitable node
3. Pod is assigned to node
4. Kubelet creates the Pod

---

# Scheduling Flow

```text
Pod Created
      |
      v
API Server
      |
      v
Scheduler
      |
      v
Choose Best Node
      |
      v
Pod Running
```

---

# Methods of Scheduling

1. NodeName
2. NodeSelector
3. Taints and Tolerations
4. Node Affinity
5. Pod Affinity
6. Pod Anti-Affinity

This document focuses on:

- NodeName
- NodeSelector
- Taints and Tolerations

---

# 1. NodeName

## Concept

NodeName directly assigns a Pod to a specific node.

Scheduler is bypassed.

Kubernetes places Pod directly on the mentioned node.

---

## Diagram

```text
Pod
 |
 | nodeName=node-1
 |
 v

+-----------+
|  Node-1   |
+-----------+
```

---

## Use Cases

- Testing
- Lab environments
- Special hardware
- GPU nodes

---

## NodeName YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: kubeserve

spec:

  # Create 2 replicas
  replicas: 2

  selector:
    matchLabels:
      app: kubeserve

  template:

    metadata:
      labels:
        app: kubeserve

    spec:

      # Force pod scheduling on this node
      nodeName: gke-cluster-1-default-pool-e18bdb3b-3h5s

      containers:

      - name: app

        # Application image
        image: leaddevops/kubeserve:v1
```

---

## Verify Pod Placement

```bash
# Check pod node assignment
kubectl get pods -o wide
```

---

## Disadvantages

- Not flexible
- Node failure causes issues
- Not suitable for large clusters

---

# 2. NodeSelector

## Concept

NodeSelector schedules Pods using labels.

Instead of choosing exact node:

```text
Choose any node matching label
```

---

# Step 1: Add Labels

```bash
# HDD Node
kubectl label node node1 disk=hdd

# HDD Node
kubectl label node node3 disk=hdd

# SSD Node
kubectl label node node2 disk=ssd
```

---

# Verify Labels

```bash
kubectl get nodes --show-labels
```

---

# NodeSelector Architecture

```text
                Cluster

+----------------------+
| Node-1               |
| disk=hdd             |
+----------------------+

+----------------------+
| Node-2               |
| disk=ssd             |
+----------------------+

+----------------------+
| Node-3               |
| disk=hdd             |
+----------------------+

         Pod
          |
          | disk=ssd
          |
          v

      Node-2
```

---

# NodeSelector YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: kubeserve

spec:

  # Create 3 replicas
  replicas: 3

  selector:
    matchLabels:
      app: kubeserve

  template:

    metadata:
      labels:
        app: kubeserve

    spec:

      nodeSelector:

        # Run only on SSD nodes
        disk: ssd

      containers:

      - name: app

        # Application image
        image: leaddevops/kubeserve:v1
```

---

# If No Matching Node Exists

Example:

```text
Node1 -> disk=hdd
Node2 -> disk=hdd
Node3 -> disk=hdd
```

Pod:

```yaml
nodeSelector:
  disk: ssd
```

Result:

```text
Pod Status = Pending
```

---

# 3. Taints and Tolerations

## Concept

NodeSelector attracts Pods.

Taints repel Pods.

Think:

```text
NodeSelector = Attraction

Taint = Rejection
```

---

# Real World Example

Production Node:

```text
Only Production Pods Allowed
```

Prevent other Pods from running.

Use Taint.

---

# Taint Syntax

```bash
kubectl taint node node1 env=prod:NoSchedule
```

Format:

```text
key=value:effect
```

Example:

```text
env=prod:NoSchedule
```

---

# Taint Diagram

```text
+------------------------+
| Node-1                 |
| env=prod:NoSchedule    |
+------------------------+

      Rejects Pods
```

---

# What Happens?

Without toleration:

```text
Pod
 |
 v

Rejected
```

---

# Toleration

Toleration allows Pod to enter tainted node.

Think:

```text
Taint = Lock

Toleration = Key
```

---

# Toleration YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  # Pod name
  name: nginx

spec:

  tolerations:

  - key: "env"

    operator: "Equal"

    value: "prod"

    effect: "NoSchedule"

  containers:

  - name: nginx

    image: nginx
```

---

# Taint + Toleration Diagram

```text
              Taint

+------------------------+
| Node-1                 |
| env=prod:NoSchedule    |
+------------------------+

            ▲
            │
            │ Allowed
            │
            │

+------------------------+
| Pod                    |
| Toleration Exists      |
+------------------------+
```

---

# Taint Effects

## 1. NoSchedule

Most common.

Behavior:

- Existing Pods continue running
- New Pods rejected

Without toleration:

```text
New Pod -> Rejected
```

---

## Example

```bash
kubectl taint node node1 env=prod:NoSchedule
```

---

## 2. PreferNoSchedule

Soft restriction.

Scheduler tries to avoid node.

But may schedule if required.

```bash
kubectl taint node node1 env=prod:PreferNoSchedule
```

---

## Behavior

```text
Avoid Node

If necessary
Schedule Pod
```

---

## 3. NoExecute

Strongest effect.

Behavior:

- Existing Pods removed
- New Pods rejected

Example:

```bash
kubectl taint node node1 env=prod:NoExecute
```

---

# NoExecute Example

Before Taint:

```text
Node-1

Pod-A
Pod-B
Pod-C
```

Apply:

```bash
kubectl taint node node1 env=prod:NoExecute
```

Result:

```text
Pod-A removed
Pod-B removed
Pod-C removed
```

Unless Pods have toleration.

---

# Remove Taint

```bash
kubectl taint node node1 env=prod:NoSchedule-
```

Notice:

```text
Trailing hyphen (-)
```

removes taint.

---

# View Taints

```bash
kubectl describe node node1
```

---

# NodeSelector vs Taints

| Feature | NodeSelector | Taint |
|-----------|------------|--------|
| Purpose | Attract Pods | Reject Pods |
| Applied On | Pod | Node |
| Behavior | Select Node | Block Node |

---

# Taint and Toleration Flow

```text
Pod Created
      |
      v

Node Tainted?
      |
      +---- No ----> Schedule
      |
      +---- Yes
               |
               v

Has Toleration?
      |
      +---- No ----> Rejected
      |
      +---- Yes ---> Schedule
```

---

# Scheduling Summary

## NodeName

```yaml
nodeName: node1
```

Direct scheduling.

---

## NodeSelector

```yaml
nodeSelector:
  disk: ssd
```

Label based scheduling.

---

## Taint

```bash
kubectl taint node node1 env=prod:NoSchedule
```

Reject Pods.

---

## Toleration

```yaml
tolerations:
- key: env
  value: prod
  effect: NoSchedule
```

Allows Pod.

---

# Interview Questions

## Q1: Difference between NodeName and NodeSelector?

NodeName selects exact node.

NodeSelector selects nodes using labels.

---

## Q2: Difference between NodeSelector and Taints?

NodeSelector attracts Pods.

Taints repel Pods.

---

## Q3: What are NoSchedule, PreferNoSchedule and NoExecute?

NoSchedule:
Reject new Pods.

PreferNoSchedule:
Avoid scheduling.

NoExecute:
Remove existing Pods and reject new Pods.

---

## Q4: How to remove taint?

```bash
kubectl taint node node1 env=prod:NoSchedule-
```

---

# Final Diagram

```text
                    Scheduling

                         |
        ---------------------------------
        |               |              |
        v               v              v

    NodeName      NodeSelector     Taints

        |               |              |
        v               v              v

 Specific Node    Label Match     Reject Pods

                                        |
                                        v

                                  Toleration

                                        |
                                        v

                                   Allow Pod
```


# Kubernetes Taints and Tolerations

## What are Taints and Tolerations?

Taints and Tolerations are Kubernetes scheduling mechanisms used to control which Pods can run on specific Nodes.

Think of them as:

```text
Taint       = Node repels Pods
Toleration  = Pod can tolerate the taint
```

### Real Life Example

Imagine a building:

```text
Building = Node

Security Guard = Taint

Entry Pass = Toleration
```

Without a valid pass, nobody can enter the building.

Similarly:

* Taint prevents Pods from being scheduled.
* Toleration allows specific Pods to be scheduled.

---

# Why Use Taints and Tolerations?

Common use cases:

* Dedicated nodes for production workloads
* Dedicated nodes for databases
* GPU nodes
* Isolating critical applications
* Preventing unwanted Pods from running on specific nodes

---

# How Taints Work

Apply a taint to a node:

```bash
# Apply taint to node
kubectl taint node gke-cluster-1-default-pool-7fe774cd-0m0q color=red:NoSchedule
```

Format:

```text
key=value:effect
```

Example:

```text
color=red:NoSchedule
```

Where:

| Component  | Meaning |
| ---------- | ------- |
| color      | Key     |
| red        | Value   |
| NoSchedule | Effect  |

---

# Taint Architecture

```text
                    Cluster

      +---------------------------+
      | Node-1                    |
      | color=red:NoSchedule      |
      +---------------------------+

      +---------------------------+
      | Node-2                    |
      +---------------------------+

      +---------------------------+
      | Node-3                    |
      +---------------------------+

               Pod
                |
                |
                X

     Scheduler Rejects Pod
```

---

# Verify Taints

```bash
# View node details
kubectl describe node gke-cluster-1-default-pool-7fe774cd-0m0q
```

Look for:

```text
Taints:
color=red:NoSchedule
```

---

# Effect 1: NoSchedule

## Concept

When a node is tainted with NoSchedule:

* Existing Pods continue running
* New Pods cannot be scheduled

Example:

```bash
# Apply NoSchedule taint
kubectl taint node gke-cluster-1-default-pool-7fe774cd-0m0q color=red:NoSchedule
```

---

## Before Taint

```text
Node-1
 ├── Pod-A
 ├── Pod-B

Node-2
 ├── Pod-C
```

---

## After Taint

```text
Node-1
 ├── Pod-A
 ├── Pod-B

Node-2
 ├── Pod-C

New Pods
   X
Rejected
```

Existing Pods remain.

New Pods are not scheduled.

---

# Remove Taint

```bash
# Remove taint
kubectl taint node gke-cluster-1-default-pool-7fe774cd-0m0q color=red:NoSchedule-
```

Notice:

```text
Trailing hyphen (-)
```

removes the taint.

---

# Effect 2: NoExecute

## Concept

NoExecute is stronger than NoSchedule.

When a node is tainted with NoExecute:

* Existing Pods are evicted
* New Pods are rejected

Example:

```bash
# Apply NoExecute taint
kubectl taint node gke-cluster-1-default-pool-7fe774cd-0m0q color=red:NoExecute
```

---

# Before NoExecute

```text
Node-1
 ├── Pod-A
 ├── Pod-B
 ├── Pod-C
```

---

# After NoExecute

```text
Node-1
   |
   X
Pods Removed

Node-2
 ├── Pod-A
 ├── Pod-B
 ├── Pod-C
```

Pods are terminated and recreated on other nodes.

---

# Demonstration

Create deployment:

```bash
# Create deployment
kubectl create -f deployment.yml
```

Verify:

```bash
# Check pods
kubectl get pods -o wide
```

Pods should be running across all nodes.

Apply taint:

```bash
# Apply NoExecute taint
kubectl taint node gke-cluster-1-default-pool-7fe774cd-0m0q color=red:NoExecute
```

Observe:

```bash
# Watch pods
kubectl get pods -o wide -w
```

Pods from the tainted node will be terminated and recreated elsewhere.

---

# What is Toleration?

Toleration allows a Pod to run on a tainted node.

Think:

```text
Taint = Lock

Toleration = Key
```

Without toleration:

```text
Pod
 |
 X
Rejected
```

With toleration:

```text
Pod
 |
 ✓
Allowed
```

---

# Step 1: Apply Taint

```bash
# Taint node
kubectl taint node gke-cluster-1-default-pool-7fe774cd-0m0q color=red:NoSchedule
```

---

# Step 2: Create Deployment with Toleration

## Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: kubeserve1

spec:

  # Create 5 replicas
  replicas: 5

  selector:
    matchLabels:
      app: kubeserve

  template:

    metadata:
      labels:
        app: kubeserve

    spec:

      tolerations:

        # Tolerate nodes having color=red
        - key: color

          # Match exact key/value
          operator: "Equal"

          # Expected value
          value: "red"

          # Match NoSchedule taint
          effect: "NoSchedule"

      containers:

      - name: app

        # Application image
        image: leaddevops/kubeserve:v1
```

---

# How Toleration Works

Node:

```text
color=red:NoSchedule
```

Pod:

```yaml
tolerations:
- key: color
  value: red
  effect: NoSchedule
```

Result:

```text
Pod
 |
 ✓
Scheduled Successfully
```

---

# Taint and Toleration Matching

Node Taint:

```text
color=red:NoSchedule
```

Pod Toleration:

```yaml
tolerations:
- key: color
  value: red
  effect: NoSchedule
```

Match occurs:

```text
key     ✓
value   ✓
effect  ✓
```

Pod is scheduled.

---

# Architecture Diagram

```text
                    Tainted Node

        +--------------------------------+
        | Node-1                         |
        | color=red:NoSchedule           |
        +--------------------------------+
                     ▲
                     │
                     │
                     │ Toleration Matches
                     │
        +--------------------------------+
        | Pod                            |
        | key=color                      |
        | value=red                      |
        | effect=NoSchedule              |
        +--------------------------------+

                 Pod Scheduled
```

---

# NoSchedule vs NoExecute

| Feature       | NoSchedule       | NoExecute |
| ------------- | ---------------- | --------- |
| Existing Pods | Continue Running | Removed   |
| New Pods      | Rejected         | Rejected  |
| Severity      | Medium           | High      |

---

# Taints and Tolerations Flow

```text
Pod Created
      |
      v

Node Tainted?
      |
      +---- No ----> Schedule Pod
      |
      +---- Yes
                |
                v

Matching Toleration?
      |
      +---- No ----> Reject Pod
      |
      +---- Yes ---> Schedule Pod
```

---

# Important Interview Questions

## Q1: What is a Taint?

A taint is applied to a node to prevent Pods from being scheduled.

Example:

```bash
kubectl taint node node1 color=red:NoSchedule
```

---

## Q2: What is a Toleration?

A toleration allows a Pod to run on a tainted node.

---

## Q3: Difference Between NoSchedule and NoExecute?

### NoSchedule

* Existing Pods remain
* New Pods rejected

### NoExecute

* Existing Pods removed
* New Pods rejected

---

## Q4: How to Remove a Taint?

```bash
kubectl taint node node1 color=red:NoSchedule-
```

---

# Summary

## Taint

Applied on Node.

Example:

```bash
kubectl taint node node1 color=red:NoSchedule
```

Purpose:

```text
Reject Pods
```

---

## Toleration

Applied on Pod.

Example:

```yaml
tolerations:
- key: color
  value: red
  effect: NoSchedule
```

Purpose:

```text
Allow Pod on Tainted Node
```

---

# Final Diagram

```text
Node
 |
 | Taint
 |
 v

Reject Pods
      |
      v

Toleration Exists?
      |
      +---- No ----> Reject
      |
      +---- Yes ---> Schedule

Pod Running
```

# Kubernetes Affinity and Anti-Affinity

## Introduction

Kubernetes Scheduler automatically decides where Pods should run.

Sometimes we want more control over scheduling to improve:

* Application Performance
* High Availability
* Fault Tolerance
* Network Latency
* Resource Utilization

Kubernetes provides **Affinity Rules** for this purpose.

Affinity rules allow Pods to be scheduled based on:

* Node Labels
* Other Pods

---

# Types of Affinity Rules

There are three main types:

## 1. Node Affinity

Controls which nodes a Pod can run on.

Example:

```text
Run Pod only on SSD nodes
```

---

## 2. Pod Affinity

Schedules Pods close to other Pods.

Example:

```text
Run WordPress on the same node as MySQL
```

Benefits:

* Faster communication
* Reduced network latency
* Better performance

---

## 3. Pod Anti-Affinity

Schedules Pods away from other Pods.

Example:

```text
Run WordPress on a different node from MySQL
```

Benefits:

* High availability
* Better fault tolerance

---

# Node Affinity

Node Affinity works using node labels.

Example:

```bash
# Add label to node
kubectl label node node1 disk=ssd
```

---

# Types of Node Affinity

## 1. Required Node Affinity

Mandatory rule.

If no matching node exists:

```text
Pod remains Pending
```

---

## Required Node Affinity YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  # Pod name
  name: with-node-affinity

spec:

  affinity:

    nodeAffinity:

      # Mandatory scheduling rule
      requiredDuringSchedulingIgnoredDuringExecution:

        nodeSelectorTerms:

        - matchExpressions:

          - key: disk

            # Label must match
            operator: In

            values:

            # Pod can run only on SSD nodes
            - ssd

  containers:

  - name: c1

    # Container image
    image: nginx
```

---

# How Required Node Affinity Works

Cluster:

```text
Node-1 -> disk=ssd
Node-2 -> disk=hdd
Node-3 -> disk=hdd
```

Result:

```text
Pod
 |
 v
Node-1
```

Only Node-1 matches.

---

# Required Affinity Diagram

```text
                 Cluster

+-------------------------+
| Node-1                  |
| disk=ssd                |
+-------------------------+

+-------------------------+
| Node-2                  |
| disk=hdd                |
+-------------------------+

+-------------------------+
| Node-3                  |
| disk=hdd                |
+-------------------------+

         Pod
          |
          |
          v

       Node-1
```

---

# 2. Preferred Node Affinity

Soft rule.

Scheduler tries to satisfy it.

If not possible:

```text
Pod will still be scheduled
```

---

# Preferred Node Affinity YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  # Pod name
  name: with-node-affinity

spec:

  affinity:

    nodeAffinity:

      preferredDuringSchedulingIgnoredDuringExecution:

      # Low priority preference
      - weight: 1

        preference:

          matchExpressions:

          - key: disk

            operator: In

            values:

            - ssd

      # High priority preference
      - weight: 50

        preference:

          matchExpressions:

          - key: disk

            operator: In

            values:

            - hdd

  containers:

  - name: c1

    # Container image
    image: nginx
```

---

# Understanding Weights

Weights range:

```text
1 - 100
```

Higher weight = Higher preference.

Example:

```text
disk=hdd weight=50

disk=ssd weight=1
```

Scheduler prefers:

```text
HDD Node
```

because weight is higher.

---

# Preferred Affinity Diagram

```text
Node-1 -> disk=ssd (weight=1)

Node-2 -> disk=hdd (weight=50)

Node-3 -> disk=hdd (weight=50)

Scheduler Preference:

Node-2 or Node-3
```

---

# Pod Affinity

## Concept

Pod Affinity schedules Pods together.

Example:

```text
WordPress should run on the same node as MySQL
```

Benefits:

* Faster communication
* Reduced latency
* Better database performance

---

# Architecture

```text
+------------------------+
| Node-1                 |
|                        |
|  MySQL Pod             |
|  WordPress Pod         |
|                        |
+------------------------+
```

Both Pods are on the same node.

---

# MySQL Deployment

## mysql.yml

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: mysql

spec:

  replicas: 1

  selector:
    matchLabels:
      app: mysql-wordpress

  template:

    metadata:
      labels:
        app: mysql-wordpress

        # Used by Pod Affinity
        product: mysql

    spec:

      containers:

      - name: mysql-container

        # MySQL image
        image: mysql

        env:

        # Password from Secret
        - name: MYSQL_ROOT_PASSWORD

          valueFrom:
            secretKeyRef:

              name: mysql-pwd
              key: password

        # Database name from ConfigMap
        - name: MYSQL_DATABASE

          valueFrom:
            configMapKeyRef:

              name: mysql-config
              key: MYSQL_DATABASE
```

---

# WordPress Pod Affinity

## wordpress.yml

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: wordpress

spec:

  replicas: 1

  selector:
    matchLabels:
      app: mysql-wordpress
      tier: frontend

  template:

    metadata:
      labels:
        app: mysql-wordpress
        tier: frontend

    spec:

      affinity:

        podAffinity:

          requiredDuringSchedulingIgnoredDuringExecution:

          - labelSelector:

              matchExpressions:

              - key: product

                operator: In

                values:

                # Find MySQL Pods
                - mysql

            # Same Node
            topologyKey: kubernetes.io/hostname

      containers:

      - name: wordpress-container

        image: wordpress

        env:

        # DB Host from ConfigMap
        - name: WORDPRESS_DB_HOST

          valueFrom:
            configMapKeyRef:

              name: mysql-config
              key: WORDPRESS_DB_HOST

        # DB User from ConfigMap
        - name: WORDPRESS_DB_USER

          valueFrom:
            configMapKeyRef:

              name: mysql-config
              key: WORDPRESS_DB_USER

        # DB Password from Secret
        - name: WORDPRESS_DB_PASSWORD

          valueFrom:
            secretKeyRef:

              name: mysql-pwd
              key: password
```

---

# How Pod Affinity Works

Scheduler searches for:

```text
product=mysql
```

Then places WordPress Pod on the same node.

---

# Pod Affinity Diagram

```text
+--------------------------+
| Node-1                   |
|                          |
| MySQL Pod                |
| Label: product=mysql     |
|                          |
| WordPress Pod            |
|                          |
+--------------------------+
```

---

# topologyKey Explanation

```yaml
topologyKey: kubernetes.io/hostname
```

Means:

```text
Same Node
```

Other examples:

```text
topology.kubernetes.io/zone
```

Means:

```text
Same Availability Zone
```

---

# Pod Anti-Affinity

## Concept

Pod Anti-Affinity schedules Pods away from each other.

Example:

```text
Do not place WordPress on the same node as MySQL
```

Benefits:

* High Availability
* Fault Isolation
* Better Resilience

---

# Pod Anti-Affinity YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: wordpress

spec:

  replicas: 1

  selector:
    matchLabels:
      app: mysql-wordpress
      tier: frontend

  template:

    metadata:
      labels:
        app: mysql-wordpress
        tier: frontend

    spec:

      affinity:

        podAntiAffinity:

          requiredDuringSchedulingIgnoredDuringExecution:

          - labelSelector:

              matchExpressions:

              - key: product

                operator: In

                values:

                # Avoid MySQL Pods
                - mysql

            # Different Node
            topologyKey: kubernetes.io/hostname

      containers:

      - name: wordpress-container

        image: wordpress
```

---

# Pod Anti-Affinity Diagram

```text
+----------------------+
| Node-1               |
|                      |
| MySQL Pod            |
+----------------------+

+----------------------+
| Node-2               |
|                      |
| WordPress Pod        |
+----------------------+
```

Pods are separated.

---

# Affinity vs Anti-Affinity

| Feature      | Pod Affinity       | Pod Anti-Affinity  |
| ------------ | ------------------ | ------------------ |
| Purpose      | Keep Pods Together | Keep Pods Apart    |
| Performance  | Better             | Normal             |
| Availability | Lower              | Higher             |
| Use Case     | App + Database     | Replica Separation |

---

# Interview Questions

## Q1: Difference between Node Affinity and Pod Affinity?

Node Affinity:

```text
Pod chooses Node
```

Pod Affinity:

```text
Pod chooses another Pod
```

---

## Q2: Difference between Required and Preferred Affinity?

Required:

```text
Mandatory
```

Preferred:

```text
Best Effort
```

---

## Q3: What does topologyKey do?

Defines the scheduling boundary.

Example:

```yaml
topologyKey: kubernetes.io/hostname
```

Means:

```text
Node level scheduling
```

---

# Summary

## Node Affinity

Controls which nodes Pods can run on.

---

## Pod Affinity

Places Pods together.

Example:

```text
WordPress + MySQL
```

---

## Pod Anti-Affinity

Places Pods apart.

Example:

```text
Replica Separation
```

---

# Final Architecture

```text
                    Affinity Rules

                          |
      -----------------------------------------
      |                  |                    |
      v                  v                    v

 Node Affinity     Pod Affinity      Pod Anti-Affinity

      |                  |                    |

 Select Node      Place Together       Place Apart

      |                  |                    |

 Performance       Low Latency       High Availability
```

# Kubernetes Networking, CoreDNS and Network Policies

## Introduction

Kubernetes networking allows Pods and Services to communicate with each other inside the cluster.

Kubernetes provides:

* Service Discovery
* DNS Resolution
* Cross Namespace Communication
* Traffic Control using Network Policies

The major components involved are:

1. CoreDNS
2. Services
3. Network Policies

---

# Service Communication Across Namespaces

## Communication Within Same Namespace

When Pods and Services are in the same namespace, communication is simple.

Example:

```text
Namespace: dev

Service Name: mongodb
```

A Pod can connect using:

```text
mongodb
```

No namespace specification is required.

---

## Communication Across Different Namespaces

Suppose:

```text
Namespace: prod
Service: mongodb
```

A Pod exists in:

```text
Namespace: dev
```

Using:

```text
mongodb
```

will fail because Kubernetes searches only within the current namespace.

Instead use:

```text
mongodb.prod
```

or Fully Qualified Domain Name (FQDN):

```text
mongodb.prod.svc.cluster.local
```

---

## Cross Namespace Communication Diagram

```text
+----------------------+
| Namespace: dev       |
|                      |
| Spring Boot Pod      |
+----------+-----------+
           |
           |
           v
mongodb.prod.svc.cluster.local
           |
           v
+----------------------+
| Namespace: prod      |
|                      |
| MongoDB Service      |
+----------------------+
```

---

# CoreDNS

## What is CoreDNS?

CoreDNS is Kubernetes' built-in DNS server.

It automatically creates DNS records for:

* Services
* Pods

This allows communication using names instead of IP addresses.

---

## Why CoreDNS?

Without DNS:

```text
Application
     |
     v
10.96.25.18
```

IP address may change.

With CoreDNS:

```text
Application
     |
     v
mongodb
```

Service name remains constant.

---

## CoreDNS Architecture

```text
                 CoreDNS

                     |
      ---------------------------------
      |                               |
      v                               v

 Service DNS                    Pod DNS

 mongodb.default            pod-ip.default
 svc.cluster.local          pod.cluster.local
```

---

# How CoreDNS Works

When a Service is created:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: mongodb
```

CoreDNS automatically creates:

```text
mongodb.default.svc.cluster.local
```

---

# DNS Configuration Inside Pods

Every Pod contains:

```bash
cat /etc/resolv.conf
```

Example:

```text
search default.svc.cluster.local
search svc.cluster.local
search cluster.local
```

These search entries are automatically configured by Kubelet.

---

## Why Search Domains Matter

Suppose Service:

```text
mongodb
```

Namespace:

```text
default
```

Pod can simply use:

```text
mongodb
```

because Kubernetes automatically expands it to:

```text
mongodb.default.svc.cluster.local
```

---

# DNS Records

## Objects That Get DNS Records

Kubernetes creates DNS records for:

1. Services
2. Pods

---

## Service DNS Records

Format:

```text
service-name.namespace.svc.cluster.local
```

Example:

```text
mongodb.default.svc.cluster.local
```

This resolves to:

```text
Cluster IP
```

Example:

```text
10.96.25.18
```

---

## Pod DNS Records

Format:

```text
pod-ip.namespace.pod.cluster.local
```

Example:

```text
172-17-0-3.default.pod.cluster.local
```

IP:

```text
172.17.0.3
```

becomes:

```text
172-17-0-3
```

---

# Pod Hostname and Subdomain

## Default Hostname

By default:

```text
Hostname = metadata.name
```

Example:

```yaml
metadata:
  name: nginx-pod
```

Hostname:

```text
nginx-pod
```

---

## Custom Hostname

You can override hostname.

Example:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:

  # Custom hostname
  hostname: my-host

  containers:
  - name: nginx
    image: nginx
```

Hostname becomes:

```text
my-host
```

---

## Subdomain

Subdomain groups Pods together.

Example:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:

  # Custom hostname
  hostname: foo

  # Subdomain
  subdomain: bar

  containers:
  - name: nginx
    image: nginx
```

---

## Generated FQDN

Namespace:

```text
my-namespace
```

Hostname:

```text
foo
```

Subdomain:

```text
bar
```

Generated FQDN:

```text
foo.bar.my-namespace.svc.cluster.local
```

---

# Network Policies

## What are Network Policies?

Network Policies control network traffic between Pods.

They act like firewall rules for Kubernetes Pods.

By default:

```text
All Pods Can Communicate
```

Network Policies allow:

* Deny Traffic
* Allow Traffic
* Restrict Communication

---

# Why Use Network Policies?

Example:

```text
Spring Boot Application
        |
        |
        v
MongoDB
```

Only Spring Boot should access MongoDB.

Other Pods should not.

Network Policies make this possible.

---

# Network Policy Architecture

```text
                 Cluster

     +--------------------------+
     | Spring Boot Pod          |
     +------------+-------------+
                  |
                  | Allowed
                  |
                  v

     +--------------------------+
     | MongoDB Pod              |
     +--------------------------+

     +--------------------------+
     | Random Pod               |
     +------------+-------------+
                  |
                  X
               Blocked
```

---

# Deny All Traffic

Create a Network Policy with empty ingress rules.

Example:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:
  name: deny-all

spec:

  # Select all pods
  podSelector: {}

  policyTypes:
  - Ingress

  # No ingress rules
  ingress: []
```

---

## Result

```text
All Incoming Traffic Blocked
```

---

# Allow Traffic from Specific Pods

Allow only Pods with a specific label.

Example:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:
  name: allow-springboot

spec:

  podSelector:
    matchLabels:
      app: mongodb

  policyTypes:
  - Ingress

  ingress:

  - from:

    - podSelector:

        matchLabels:
          app: springboot
```

---

## Result

```text
Spring Boot → MongoDB ✓

Other Pods → MongoDB ✗
```

---

# Allow Traffic from Specific Namespace

Example:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:
  name: allow-dev-namespace

spec:

  podSelector:
    matchLabels:
      app: mongodb

  ingress:

  - from:

    - namespaceSelector:

        matchLabels:
          name: dev
```

---

## Result

```text
Pods From Namespace dev
           |
           v
        Allowed

Other Namespaces
           |
           X
        Blocked
```

---

# Allow Traffic from Specific IP Address

Using CIDR blocks.

Example:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:
  name: allow-specific-ip

spec:

  podSelector:
    matchLabels:
      app: mongodb

  ingress:

  - from:

    - ipBlock:

        # Allowed IP range
        cidr: 192.168.1.0/24
```

---

## Result

```text
192.168.1.x → Allowed

Others → Blocked
```

---

# Network Policy Flow

```text
Incoming Request
        |
        v

Network Policy Exists?
        |
        +---- No ----> Allow
        |
        +---- Yes
                |
                v

Rule Matches?
        |
        +---- Yes ---> Allow
        |
        +---- No ----> Deny
```

---

# CoreDNS + Network Policy Example

```text
Spring Boot Pod
       |
       |
       v

mongodb.default.svc.cluster.local
       |
       |
       v

CoreDNS Resolves Service
       |
       v

MongoDB Service
       |
       v

Network Policy Check
       |
       +---- Allowed -> Connect
       |
       +---- Denied  -> Block
```

---

# Interview Questions

## Q1: What is CoreDNS?

CoreDNS is Kubernetes' DNS server that creates DNS records for Services and Pods.

---

## Q2: How do Services communicate across namespaces?

Using:

```text
service-name.namespace.svc.cluster.local
```

Example:

```text
mongodb.prod.svc.cluster.local
```

---

## Q3: What objects get DNS records?

* Services
* Pods

---

## Q4: What is a Network Policy?

A Kubernetes resource used to control network traffic between Pods.

---

## Q5: What happens if no Network Policy exists?

By default:

```text
All traffic is allowed
```

---

## Q6: Can Network Policies allow traffic from specific namespaces?

Yes.

Using:

```yaml
namespaceSelector:
```

---

# Summary

## CoreDNS

Provides DNS records for:

* Services
* Pods

---

## Service Discovery

Service FQDN:

```text
service.namespace.svc.cluster.local
```

---

## Network Policies

Used to:

* Deny Traffic
* Allow Traffic
* Restrict Pod Communication

---

## Traffic Control Options

* Pod Selector
* Namespace Selector
* IP Block (CIDR)

---

# Final Architecture

```text
                 Kubernetes Cluster

                          |
       ----------------------------------------
       |                                      |
       v                                      v

     CoreDNS                         Network Policies

       |                                      |

 Resolve Service Names              Control Traffic

       |                                      |

 mongodb.default                Allow / Deny Rules

       |                                      |

       ------------------- Application ----------------
```

# Kubernetes Network Policies with Spring Boot and MongoDB

## Project Overview

In this project, we deploy:

* MongoDB Database
* Spring Boot Application
* MongoDB Service
* Spring Boot Service
* Network Policies

The objective is to understand:

* Pod-to-Pod communication
* Service communication
* Ingress traffic
* Egress traffic
* Allow and Deny rules using Network Policies

---

# Application Architecture

```text
                    Internet User
                           |
                           |
                           v
                +---------------------+
                | Spring Boot Service |
                |     NodePort        |
                +----------+----------+
                           |
                           |
                           v
                +---------------------+
                | Spring Boot Pods    |
                | app=myapp           |
                +----------+----------+
                           |
                           | Egress
                           |
                           v
                +---------------------+
                | MongoDB Service     |
                |     ClusterIP       |
                +----------+----------+
                           |
                           |
                           v
                +---------------------+
                | MongoDB Pod         |
                | app=mongodb         |
                +---------------------+

```

---

# Deploy MongoDB

MongoDB acts as the backend database for the Spring Boot application.

## MongoDB Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  # Deployment name
  name: mongodb

  labels:
    app: mongodb

spec:

  # Number of MongoDB pods
  replicas: 1

  selector:
    matchLabels:
      app: mongodb

  template:

    metadata:
      labels:
        app: mongodb

    spec:

      containers:

      - name: mongo

        # MongoDB image
        image: lerndevops/samples:mongodb
```

---

## MongoDB Service YAML

```yaml
apiVersion: v1
kind: Service

metadata:

  # Service name
  name: mongo

spec:

  # Internal service
  type: ClusterIP

  ports:

  - port: 27017

    # MongoDB container port
    targetPort: 27017

  selector:

    # Select MongoDB pods
    app: mongodb
```

---

# Deploy Spring Boot Application

The Spring Boot application connects to MongoDB.

## Spring Boot Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:

  # Deployment name
  name: springboot-app

spec:

  # Create 2 replicas
  replicas: 2

  selector:
    matchLabels:
      app: myapp

  template:

    metadata:
      labels:
        app: myapp

    spec:

      containers:

      - name: springboot-app

        # Spring Boot image
        image: lerndevops/samples:springboot-app
```

---

## Spring Boot Service YAML

```yaml
apiVersion: v1
kind: Service

metadata:

  # Service name
  name: springboot-app-svc

spec:

  # Expose application externally
  type: NodePort

  ports:

  - port: 80

    # Spring Boot container port
    targetPort: 8080

  selector:

    # Select Spring Boot pods
    app: myapp
```

---

# Verify Resources

```bash
# View deployments
kubectl get deploy

# View services
kubectl get svc

# View pods
kubectl get pods

# View all resources
kubectl get all
```

---

# Understanding Network Policies

## What is a Network Policy?

A Network Policy is a Kubernetes resource that controls network communication between Pods.

Think of it as:

```text
Firewall for Pods
```

It defines:

* Which Pods can communicate
* Which traffic is allowed
* Which traffic is denied

---

# Why Use Network Policies?

Without Network Policies:

```text
All Pods Can Talk To All Pods
```

With Network Policies:

```text
Only Authorized Pods Can Communicate
```

This improves:

* Security
* Isolation
* Compliance
* Application Protection

---

# Understanding Ingress and Egress

## Ingress

Traffic entering a Pod.

Example:

```text
User
 |
 v
Spring Boot Pod
```

This is ingress traffic for Spring Boot.

---

## Egress

Traffic leaving a Pod.

Example:

```text
Spring Boot Pod
       |
       v
MongoDB Pod
```

This is egress traffic for Spring Boot.

---

## Visual Representation

```text
           Ingress
              |
              v

      +----------------+
      | Spring Boot    |
      +----------------+
              |
              |
              v
            Egress

      +----------------+
      | MongoDB        |
      +----------------+
```

---

# Deny All Incoming Traffic

First, we block all incoming traffic to MongoDB and Spring Boot.

## Deny MongoDB Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:

  # Policy name
  name: deny-mongodb-ingress-from-all

  namespace: default

spec:

  podSelector:

    matchLabels:
      app: mongodb

  policyTypes:

  # Apply only ingress rules
  - Ingress
```

---

## Deny Spring Boot Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:

  # Policy name
  name: deny-springapp-ingress-from-all

  namespace: default

spec:

  podSelector:

    matchLabels:
      app: myapp

  policyTypes:

  - Ingress
```

---

## Result

```text
Spring Boot  -> Blocked

MongoDB      -> Blocked
```

No incoming traffic is allowed.

---

# Allow Spring Boot to Access MongoDB

We want only Spring Boot pods to connect to MongoDB.

## MongoDB Allow Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:

  # Policy name
  name: allow-mongodb-ingress-from-springapp

  namespace: default

spec:

  podSelector:

    # Apply to MongoDB Pods
    matchLabels:
      app: mongodb

  policyTypes:

  - Ingress

  ingress:

  - from:

    # Allow only Spring Boot pods
    - podSelector:

        matchLabels:
          app: myapp
```

---

## Communication Flow

```text
Spring Boot Pod
       |
       |
       v
MongoDB Pod

Allowed
```

---

## Blocked Communication

```text
Random Pod
     |
     |
     X
MongoDB

Blocked
```

---

# Allow Traffic to Spring Boot

Spring Boot should accept traffic from anywhere.

## Spring Boot Allow Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:

  # Policy name
  name: allow-springapp-ingress-from-all

  namespace: default

spec:

  podSelector:

    matchLabels:
      app: myapp

  policyTypes:

  - Ingress

  ingress:

  - from:

    - ipBlock:

        # Allow all IP addresses
        cidr: 0.0.0.0/0
```

---

## Result

```text
Internet
    |
    v
Spring Boot

Allowed
```

---

# Allow Traffic from a Specific Namespace

Sometimes only Pods from a particular namespace should communicate.

---

## Create Namespace with Label

```bash
# Create namespace
kubectl create namespace test
```

Add label:

```bash
# Add label to namespace
kubectl label namespace test app=test
```

---

## Create Namespace Network Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:

  # Policy name
  name: allow-traffic-in-ns

  namespace: test

spec:

  # Select all pods
  podSelector: {}

  policyTypes:

  - Ingress

  ingress:

  - from:

    - namespaceSelector:

        matchLabels:

          app: test
```

---

## Result

```text
Namespace test
      |
      v
 Allowed

Other Namespaces
      |
      X
 Blocked
```

---

# Default Deny All Egress Traffic

This policy blocks all outgoing traffic.

## Egress Deny Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:

  # Policy name
  name: default-deny-egress

spec:

  # Select all pods
  podSelector: {}

  policyTypes:

  - Egress
```

---

## Result

```text
Pod
 |
 X
Cannot Connect Anywhere
```

---

# Default Deny All Traffic

This policy blocks:

* All ingress traffic
* All egress traffic

---

## Default Deny All Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:

  # Policy name
  name: default-deny-all

spec:

  # Select all pods
  podSelector: {}

  policyTypes:

  - Ingress

  - Egress
```

---

## Result

```text
All Incoming Traffic  -> Blocked

All Outgoing Traffic  -> Blocked
```

---

# Multi-Port Egress Policy

Allows Pods to communicate only on a specific port range.

## Example Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy

metadata:

  # Policy name
  name: multi-port-egress

  namespace: default

spec:

  podSelector:

    matchLabels:

      # Apply to database pods
      role: db

  policyTypes:

  - Egress

  egress:

  - to:

    - ipBlock:

        # Allowed subnet
        cidr: 10.0.0.0/24

    ports:

    - protocol: TCP

      # Starting port
      port: 32000

      # Ending port
      endPort: 32768
```

---

## What This Policy Does

Allows:

```text
Pods with role=db
       |
       v
10.0.0.0/24
       |
       v
TCP Ports 32000-32768
```

---

# Network Policy Processing Flow

```text
Request Arrives
        |
        v

Network Policy Exists?
        |
        +---- No ----> Allow
        |
        +---- Yes
                |
                v

Rule Matches?
        |
        +---- Yes ---> Allow
        |
        +---- No ----> Deny
```

---

# Interview Questions

## Q1. What is a Network Policy?

A Network Policy is a Kubernetes resource used to control Pod-to-Pod communication.

---

## Q2. What is Ingress?

Traffic entering a Pod.

Example:

```text
User -> Spring Boot
```

---

## Q3. What is Egress?

Traffic leaving a Pod.

Example:

```text
Spring Boot -> MongoDB
```

---

## Q4. What happens if no Network Policy exists?

By default:

```text
All traffic is allowed.
```

---

## Q5. Can Network Policies allow traffic from specific namespaces?

Yes.

Using:

```yaml
namespaceSelector:
```

---

## Q6. Can Network Policies allow traffic from specific IP ranges?

Yes.

Using:

```yaml
ipBlock:
  cidr: 192.168.1.0/24
```

---

# Summary

## Spring Boot

* Exposed through NodePort
* Accessible from external users

## MongoDB

* Exposed through ClusterIP
* Accessible only inside cluster

## Network Policies

Used to:

* Deny Traffic
* Allow Traffic
* Restrict Communication
* Improve Security

## Traffic Filters

* Pod Selector
* Namespace Selector
* IP Block

## Best Practice

```text
Default Deny All
       |
       v
Allow Only Required Traffic
```

This follows the Principle of Least Privilege and is the recommended approach for production Kubernetes clusters.



# Kubernetes DaemonSet

## Introduction

A **DaemonSet** is a Kubernetes workload object that ensures **one Pod runs on every node** in the cluster.

Unlike a Deployment, where you specify the number of replicas, a DaemonSet automatically creates **one Pod per node**.

Whenever:

* A new node joins the cluster → a new DaemonSet Pod is created automatically.
* A node is removed from the cluster → the corresponding DaemonSet Pod is removed automatically.

---

# Why Use DaemonSets?

DaemonSets are commonly used for applications that need to run on **every node**.

Examples:

| Use Case           | Example                  |
| ------------------ | ------------------------ |
| Antivirus          | ClamAV                   |
| Monitoring Agent   | Prometheus Node Exporter |
| Log Collection     | Fluentd                  |
| Security Agent     | Falco                    |
| Networking Plugin  | Calico                   |
| Metrics Collection | Datadog Agent            |

---

# DaemonSet Architecture

```text
                    Kubernetes Cluster

        +-----------------------------------+
        | Node-1                            |
        |                                   |
        | DaemonSet Pod (ClamAV)            |
        +-----------------------------------+

        +-----------------------------------+
        | Node-2                            |
        |                                   |
        | DaemonSet Pod (ClamAV)            |
        +-----------------------------------+

        +-----------------------------------+
        | Node-3                            |
        |                                   |
        | DaemonSet Pod (ClamAV)            |
        +-----------------------------------+
```

Notice:

```text
1 Node = 1 DaemonSet Pod
```

---

# Difference Between Deployment and DaemonSet

## Deployment

Suppose:

```yaml
replicas: 3
```

Kubernetes creates exactly:

```text
3 Pods
```

These Pods can run on any nodes.

Example:

```text
Node-1 → 2 Pods
Node-2 → 1 Pod
Node-3 → 0 Pods
```

---

## DaemonSet

Kubernetes creates:

```text
1 Pod Per Node
```

Example:

```text
Node-1 → 1 Pod
Node-2 → 1 Pod
Node-3 → 1 Pod
```

No replica count is needed.

---

# DaemonSet YAML

## DaemonSet Definition

```yaml
apiVersion: apps/v1

# Kubernetes object type
kind: DaemonSet

metadata:

  # DaemonSet name
  name: myds

spec:

  selector:

    matchLabels:

      # Select Pods with this label
      app: anti-virus

  template:

    metadata:

      labels:

        # Label assigned to Pods
        app: anti-virus

    spec:

      containers:

      - name: c1

        # ClamAV Antivirus image
        image: clamav/clamav
```

---

# Explanation of YAML

## apiVersion

```yaml
apiVersion: apps/v1
```

Defines the API version used by the DaemonSet.

---

## kind

```yaml
kind: DaemonSet
```

Tells Kubernetes to create a DaemonSet object.

---

## metadata

```yaml
metadata:
  name: myds
```

Name of the DaemonSet.

---

## selector

```yaml
selector:
  matchLabels:
    app: anti-virus
```

Used by Kubernetes to identify Pods managed by the DaemonSet.

---

## template

```yaml
template:
```

Defines the Pod template.

This template is used to create Pods on every node.

---

## labels

```yaml
labels:
  app: anti-virus
```

Label assigned to every DaemonSet Pod.

---

## container

```yaml
containers:
- name: c1
  image: clamav/clamav
```

Container configuration.

Here we are running:

```text
ClamAV Antivirus
```

on every node.

---

# Create DaemonSet

Save the file:

```bash
# Create YAML file
vim DaemonSet.yml
```

Apply:

```bash
# Create DaemonSet
kubectl create -f DaemonSet.yml
```

---

# Verify DaemonSet

Check DaemonSet:

```bash
# List DaemonSets
kubectl get daemonsets
```

Example:

```text
NAME   DESIRED   CURRENT   READY
myds      3         3        3
```

Meaning:

| Field   | Meaning         |
| ------- | --------------- |
| DESIRED | Number of nodes |
| CURRENT | Running Pods    |
| READY   | Healthy Pods    |

---

# Check Pods

```bash
# View pods
kubectl get pods -o wide
```

Example:

```text
NAME               NODE
myds-abc12         node-1
myds-def34         node-2
myds-ghi56         node-3
```

Notice:

```text
One Pod Per Node
```

---

# DaemonSet Scheduling Diagram

```text
                 DaemonSet

                      |
                      |
                      v

        +--------------------------+
        | Node-1                   |
        | ClamAV Pod               |
        +--------------------------+

        +--------------------------+
        | Node-2                   |
        | ClamAV Pod               |
        +--------------------------+

        +--------------------------+
        | Node-3                   |
        | ClamAV Pod               |
        +--------------------------+
```

---

# Adding a New Node

Suppose the cluster initially has:

```text
Node-1
Node-2
Node-3
```

DaemonSet Pods:

```text
Pod-1
Pod-2
Pod-3
```

---

## Before Adding Node

```text
Node-1 → ClamAV Pod
Node-2 → ClamAV Pod
Node-3 → ClamAV Pod
```

---

## Add New Node

```text
Node-4 Joined Cluster
```

Kubernetes automatically detects:

```text
New Node Available
```

DaemonSet automatically creates:

```text
ClamAV Pod on Node-4
```

---

## After Adding Node

```text
Node-1 → ClamAV Pod
Node-2 → ClamAV Pod
Node-3 → ClamAV Pod
Node-4 → ClamAV Pod
```

No manual action required.

---

# Automatic Scaling Behavior

Deployment:

```text
Manual Scaling Required
```

Example:

```bash
kubectl scale deployment app --replicas=5
```

---

DaemonSet:

```text
Automatic Scaling Based On Nodes
```

Add a node:

```text
New Pod Created Automatically
```

Remove a node:

```text
Pod Removed Automatically
```

---

# View Detailed Information

```bash
# Describe DaemonSet
kubectl describe daemonset myds
```

Shows:

* Desired Pods
* Current Pods
* Node Assignment
* Events

---

# Common Commands

## List DaemonSets

```bash
kubectl get daemonsets
```

---

## Get Detailed Information

```bash
kubectl describe daemonset myds
```

---

## View Pods

```bash
kubectl get pods -o wide
```

---

## Delete DaemonSet

```bash
kubectl delete daemonset myds
```

---

# Real-World Use Cases

## Antivirus

```text
ClamAV
```

Scans every node for malware.

---

## Monitoring

```text
Node Exporter
```

Collects CPU, Memory, Disk metrics from every node.

---

## Logging

```text
Fluentd
```

Collects logs from every node.

---

## Security

```text
Falco
```

Monitors suspicious activities on every node.

---

# Deployment vs DaemonSet

| Feature           | Deployment     | DaemonSet                 |
| ----------------- | -------------- | ------------------------- |
| Pod Count         | Fixed Replicas | One Per Node              |
| Scaling           | Manual         | Automatic                 |
| Use Case          | Applications   | Node Services             |
| New Node Handling | No Action      | Pod Created Automatically |

---

# Interview Questions

## Q1. What is a DaemonSet?

A DaemonSet ensures that a Pod runs on every node in the cluster.

---

## Q2. Do we specify replicas in a DaemonSet?

No.

Kubernetes automatically creates one Pod per node.

---

## Q3. What happens when a new node is added?

DaemonSet automatically creates a new Pod on the new node.

---

## Q4. Give examples of DaemonSet usage.

* ClamAV
* Fluentd
* Node Exporter
* Falco
* Datadog Agent

---

## Q5. Difference between Deployment and DaemonSet?

Deployment creates a specified number of replicas.

DaemonSet creates one Pod per node.

---

# Summary

## Deployment

```text
You Decide Number Of Pods
```

Example:

```yaml
replicas: 3
```

---

## DaemonSet

```text
Kubernetes Decides Number Of Pods
```

Based on:

```text
Number Of Nodes
```

---

## Key Point

```text
1 Node = 1 DaemonSet Pod
```

This makes DaemonSets ideal for:

* Monitoring
* Logging
* Security
* Networking
* Antivirus workloads

```
```


# Kubernetes StatefulSets

## Introduction

In Kubernetes, applications can be broadly classified into:

1. Stateless Applications
2. Stateful Applications

Examples:

| Stateless Applications | Stateful Applications |
| ---------------------- | --------------------- |
| Nginx                  | MySQL                 |
| Apache                 | MongoDB               |
| Spring Boot            | PostgreSQL            |
| NodeJS                 | Cassandra             |
| React App              | Redis                 |

---

# Stateless Applications (Deployments)

For stateless applications, Kubernetes uses a **Deployment**.

Characteristics:

* Multiple replicas can be created.
* Pods get random names.
* Pods are interchangeable.
* No fixed identity.
* No stable hostname.
* If a Pod is deleted, a new Pod gets a different name.
* Communication happens through Services.

---

## Deployment Example

Suppose:

```yaml
replicas: 2
```

Pods created:

```text
nginx-78c8d4fd7f-j2p6m
nginx-78c8d4fd7f-z8w9k
```

If a Pod is deleted:

```text
nginx-78c8d4fd7f-x4s9l
```

A completely new name is generated.

---

## Deployment Architecture

```text
                    Deployment

                         |
                         |
                         v

         +------------------------------+
         | nginx-78c8d4fd7f-j2p6m       |
         +------------------------------+

         +------------------------------+
         | nginx-78c8d4fd7f-z8w9k       |
         +------------------------------+

               Random Pod Names
```

---

# Why StatefulSets?

Some applications require:

* Fixed identity
* Stable hostname
* Stable DNS name
* Ordered deployment
* Ordered scaling
* Ordered deletion
* Persistent storage

Examples:

```text
MySQL
MongoDB
PostgreSQL
Redis
Kafka
ZooKeeper
Elasticsearch
```

These applications require StatefulSets.

---

# StatefulSet Features

## Stable Pod Names

Pods receive predictable names:

```text
web-0
web-1
web-2
```

Unlike Deployments:

```text
web-7d4d7c7f8c-abc12
```

---

## Stable Network Identity

Every Pod gets:

```text
web-0.nginx
web-1.nginx
web-2.nginx
```

These DNS names remain constant.

---

## Ordered Deployment

Pods are created in sequence.

Example:

```text
web-0
   |
   v
web-1
   |
   v
web-2
```

Kubernetes waits for:

```text
web-0 → Running + Ready
```

before creating:

```text
web-1
```

---

## Ordered Deletion

Pods are deleted in reverse order.

Example:

```text
web-2
   |
   v
web-1
   |
   v
web-0
```

---

# Headless Service

A StatefulSet must be associated with a **Headless Service**.

## What is a Headless Service?

A Headless Service is a Service without a ClusterIP.

Normal Service:

```text
Service Name
      |
      v
ClusterIP
      |
      v
Pods
```

Headless Service:

```text
Service Name
      |
      v
Direct DNS Records
      |
      v
Individual Pods
```

---

## Why StatefulSets Need Headless Services?

Because each Pod requires:

* Individual DNS record
* Stable hostname
* Direct access

---

# Headless Service YAML

```yaml
apiVersion: v1
kind: Service

metadata:

  # Service name
  name: nginx

  labels:
    app: nginx

spec:

  ports:

  - port: 80

    # Port name
    name: web

  # Headless Service
  clusterIP: None

  selector:

    # Select nginx Pods
    app: nginx
```

---

## Important Line

```yaml
clusterIP: None
```

This makes the Service headless.

Without this:

```text
Normal Service
```

With this:

```text
Headless Service
```

---

# StatefulSet YAML

```yaml
apiVersion: apps/v1
kind: StatefulSet

metadata:

  # StatefulSet name
  name: web

spec:

  # Headless Service name
  serviceName: "nginx"

  # Number of Pods
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

        # Nginx image
        image: k8s.gcr.io/nginx-slim:0.8

        ports:

        - containerPort: 80

          name: web
```

---

# Create StatefulSet

## Step 1

Save YAML:

```bash
vim web.yml
```

---

## Step 2

Create StatefulSet:

```bash
kubectl create -f web.yml
```

---

## Step 3

Watch Pod Creation

Open another terminal:

```bash
kubectl get pods -w -l app=nginx
```

---

# Observe Ordered Creation

Pods are created sequentially.

Example:

```text
web-0 Creating

web-0 Running

web-1 Creating

web-1 Running
```

Notice:

```text
web-1 is not created until web-0 is Ready
```

---

# Verify StatefulSet

## Check Service

```bash
kubectl get service nginx
```

---

## Check StatefulSet

```bash
kubectl get statefulset web
```

---

## Check Pods

```bash
kubectl get pods -l app=nginx
```

Output:

```text
web-0
web-1
```

---

# StatefulSet Architecture

```text
                   Headless Service

                           |
                           |
                           v

        +----------------------------------+
        | web-0.nginx                      |
        +----------------------------------+

        +----------------------------------+
        | web-1.nginx                      |
        +----------------------------------+

             Stable DNS Identities
```

---

# Stable Hostnames

Each Pod receives a hostname.

Check:

```bash
for i in 0 1; do
kubectl exec web-$i -- hostname
done
```

Output:

```text
web-0
web-1
```

---

# Stable DNS Records

StatefulSet Pods receive DNS entries.

Format:

```text
pod-name.service-name
```

Example:

```text
web-0.nginx
web-1.nginx
```

---

# DNS Testing

Create BusyBox Pod:

```bash
kubectl run -i --tty \
--image=busybox:1.28 \
dns-test \
--restart=Never \
--rm
```

---

## Inside BusyBox

Run:

```bash
nslookup web-0.nginx

nslookup web-1.nginx
```

Example Output:

```text
Name: web-0.nginx
Address: 10.244.1.7

Name: web-1.nginx
Address: 10.244.2.8
```

---

# DNS Architecture

```text
                    CoreDNS

                        |
      ------------------------------------
      |                                  |
      v                                  v

 web-0.nginx                    web-1.nginx

      |                                  |
      v                                  v

10.244.1.7                      10.244.2.8
```

---

# Deleting StatefulSet Pods

Delete Pods:

```bash
kubectl delete pod -l app=nginx
```

---

## What Happens?

Kubernetes automatically recreates them.

Watch:

```bash
kubectl get pod -w -l app=nginx
```

Output:

```text
web-0 Terminating

web-0 Running

web-1 Terminating

web-1 Running
```

---

# Important Observation

After recreation:

Pod names remain:

```text
web-0
web-1
```

Hostnames remain:

```text
web-0.nginx
web-1.nginx
```

Only IP addresses may change.

Example:

Before:

```text
web-0 = 10.244.1.7
```

After recreation:

```text
web-0 = 10.244.3.5
```

---

# Why Not Use Pod IP?

Pod IPs are temporary.

They can change whenever Pods restart.

Bad Practice:

```text
Application
     |
     v
10.244.1.7
```

Good Practice:

```text
Application
     |
     v
web-0.nginx
```

DNS remains stable.

---

# StatefulSet vs Deployment

| Feature                   | Deployment | StatefulSet |
| ------------------------- | ---------- | ----------- |
| Pod Names                 | Random     | Fixed       |
| DNS Identity              | No         | Yes         |
| Ordered Scaling           | No         | Yes         |
| Ordered Deletion          | No         | Yes         |
| Stable Hostname           | No         | Yes         |
| Suitable for Databases    | No         | Yes         |
| Headless Service Required | No         | Yes         |

---

# Real-World Use Cases

## Databases

```text
MySQL
PostgreSQL
MongoDB
Oracle
```

---

## Distributed Systems

```text
Kafka
ZooKeeper
Cassandra
Elasticsearch
Redis Cluster
```

---

# Interview Questions

## Q1. Why use StatefulSets?

StatefulSets provide:

* Stable identities
* Stable hostnames
* Stable DNS
* Ordered deployment

---

## Q2. Why do StatefulSets need a Headless Service?

To create:

```text
Per-Pod DNS Records
```

such as:

```text
web-0.nginx
web-1.nginx
```

---

## Q3. What is a Headless Service?

A Service with:

```yaml
clusterIP: None
```

---

## Q4. What happens when a StatefulSet Pod is deleted?

It is recreated with:

* Same name
* Same hostname
* Same DNS identity

---

## Q5. Can Pod IPs change?

Yes.

Pod names and DNS remain stable.

---

# Summary

## Deployment

```text
Stateless Applications
```

Examples:

```text
Nginx
Apache
Spring Boot
```

---

## StatefulSet

```text
Stateful Applications
```

Examples:

```text
MySQL
MongoDB
Kafka
Redis
```

---

## Key Point

```text
Deployment = Random Identity

StatefulSet = Fixed Identity
```

---

# Final Architecture

```text
                  StatefulSet

                        |
                        |
                        v

              Headless Service
               (clusterIP=None)

                        |
        ---------------------------------
        |                               |
        v                               v

    web-0.nginx                   web-1.nginx

        |                               |
        v                               v

   Stable DNS                     Stable DNS

        |                               |
        ---------------------------------

                Stateful Application
```


# Kubernetes Mini Project: Stateless and Stateful Applications

## Project Overview

In this mini project, we deploy:

### Stateless Application

A Node.js application deployed using a **Deployment**.

Characteristics:

* Pods are interchangeable
* Pods have random names
* Easy to scale
* No stable identity required

### Stateful Application

A Redis database deployed using a **StatefulSet**.

Characteristics:

* Stable Pod names
* Stable DNS names
* Ordered deployment
* Ordered scaling
* Stable network identity

---

# Architecture Diagram

```text
                           User/Application
                                   |
                                   |
                                   v

                     +--------------------------+
                     | Node.js Deployment       |
                     | (2 Replicas)             |
                     +------------+-------------+
                                  |
                                  |
                                  v

         redis-0.redis.default.svc.cluster.local

                                  |
                                  |
                                  v

                     +--------------------------+
                     | Headless Service         |
                     | clusterIP: None          |
                     +------------+-------------+
                                  |
                                  |
          -----------------------------------------------
          |                                             |
          v                                             v

 +--------------------+                   +--------------------+
 | Redis StatefulSet  |                   | Redis StatefulSet  |
 | redis-0            |                   | redis-1            |
 +--------------------+                   +--------------------+

 Stable Hostname                         Stable Hostname
```

---

# Understanding the Project

The project contains two applications:

| Application | Type      |
| ----------- | --------- |
| Node.js     | Stateless |
| Redis       | Stateful  |

The Node.js application writes data into Redis every 5 seconds.

---

# Why Redis Uses StatefulSet

Redis is a database.

Databases require:

* Stable hostname
* Stable DNS
* Persistent identity
* Predictable naming

Therefore:

```text
Redis → StatefulSet
```

---

# Why Node.js Uses Deployment

Node.js application:

* Does not store data locally
* Any Pod can handle requests
* Pods are interchangeable

Therefore:

```text
Node.js → Deployment
```

---

# Stateful Application: Redis

## Redis StatefulSet

File:

```bash
# Create StatefulSet YAML
vim statefulapp.yml
```

---

## Redis StatefulSet YAML

```yaml
apiVersion: apps/v1

# Stateful application
kind: StatefulSet

metadata:

  # StatefulSet name
  name: redis

spec:

  # Headless service name
  serviceName: "redis"

  # Number of Redis replicas
  replicas: 2

  selector:

    matchLabels:

      # Match Redis pods
      app: redis

  template:

    metadata:

      labels:

        # Label assigned to Redis pods
        app: redis

    spec:

      containers:

      - name: redis

        # Redis image
        image: redis:7

        ports:

        # Redis port
        - containerPort: 6379
```

---

# Redis Headless Service

## Why Headless Service?

StatefulSets require a Headless Service because:

* Every Pod gets its own DNS name
* No load balancing
* Direct Pod access

---

## Redis Service YAML

```yaml
apiVersion: v1

# Service object
kind: Service

metadata:

  # Service name
  name: redis

spec:

  ports:

  - port: 6379

  # Headless Service
  clusterIP: None

  selector:

    # Select Redis pods
    app: redis
```

---

# Important Line

```yaml
clusterIP: None
```

This converts the Service into a:

```text
Headless Service
```

---

# Redis Pod Naming

Since Redis is a StatefulSet:

Pods receive predictable names.

```text
redis-0
redis-1
```

If scaled:

```text
redis-0
redis-1
redis-2
redis-3
```

---

# Redis DNS Names

Each Redis Pod receives a stable DNS name.

Format:

```text
pod-name.service-name.namespace.svc.cluster.local
```

Examples:

```text
redis-0.redis.default.svc.cluster.local

redis-1.redis.default.svc.cluster.local
```

These names never change.

---

# StatefulSet Creation Order

Pods are created sequentially.

```text
redis-0
   |
   v
redis-1
```

Redis-1 will not start until:

```text
redis-0 is Running and Ready
```

---

# Stateless Application: Node.js

## Create Deployment

File:

```bash
# Create Deployment YAML
vim statelessapp.yml
```

---

## Node.js Deployment YAML

```yaml
apiVersion: apps/v1

# Stateless application
kind: Deployment

metadata:

  # Deployment name
  name: node-app

spec:

  # Create two replicas
  replicas: 2

  selector:

    matchLabels:

      # Match node application pods
      app: node-app

  template:

    metadata:

      labels:

        # Pod label
        app: node-app

    spec:

      containers:

      - name: node-container

        # Node.js image
        image: sonal04/mynoderedis

        command:

        # Execute node inline script
        - node
        - -e

        args:

        - |
          const redis = require('redis');

          // Connect to Redis StatefulSet Pod
          const client = redis.createClient({
            url: 'redis://redis-0.redis.default.svc.cluster.local:6379'
          });

          client.connect();

          setInterval(async () => {

            // Store current timestamp
            await client.set(
              'time',
              new Date().toISOString()
            );

            // Read timestamp
            const time =
              await client.get('time');

            console.log(
              'Redis time:',
              time
            );

          }, 5000);

        env:

        - name: NODE_ENV

          # Production environment
          value: production
```

---

# Understanding the Node.js Code

The application:

### Step 1

Connects to Redis.

```javascript
redis://redis-0.redis.default.svc.cluster.local:6379
```

---

### Step 2

Every 5 seconds:

```javascript
setInterval()
```

runs.

---

### Step 3

Stores current time in Redis.

```javascript
client.set()
```

---

### Step 4

Reads current time.

```javascript
client.get()
```

---

### Step 5

Prints result.

```javascript
console.log()
```

---

# Communication Flow

```text
Node.js Pod
      |
      |
      v

redis-0.redis.default.svc.cluster.local

      |
      |
      v

Redis Pod

      |
      |
      v

Store Timestamp
```

---

# Why Use redis-0 Instead of Service?

Normally:

```text
redis.default.svc.cluster.local
```

points to all Redis Pods.

But:

```text
redis-0.redis.default.svc.cluster.local
```

points specifically to:

```text
redis-0
```

This is useful when:

* Specific database instance required
* Master node access needed
* Replication topology exists

---

# Verify Resources

## Check StatefulSet

```bash
# View StatefulSet
kubectl get statefulset
```

---

## Check Redis Pods

```bash
# View Redis Pods
kubectl get pods
```

Expected:

```text
redis-0
redis-1
```

---

## Check Services

```bash
# View services
kubectl get svc
```

Expected:

```text
redis
```

---

## Check Node.js Pods

```bash
# View Node.js pods
kubectl get pods
```

Expected:

```text
node-app-xxxxx

node-app-yyyyy
```

Notice:

Node.js pods have random names.

Redis pods have fixed names.

---

# Test Inter-Pod Communication

Connect to Node.js Pod.

```bash
# Enter Node.js pod
kubectl exec -it node-app-xxxxx -- sh
```

---

## Install Redis Client

```bash
# Update packages
apt-get update

# Install redis tools
apt-get install redis-tools -y
```

---

## Connect to Redis

```bash
# Connect to Redis service
redis-cli -h redis -p 6379
```

---

## Test Connection

```bash
PING
```

Output:

```text
PONG
```

Successful communication confirms:

```text
Node.js
     ↔
Redis
```

communication is working.

---

# StatefulSet vs Deployment

| Feature          | Deployment      | StatefulSet |
| ---------------- | --------------- | ----------- |
| Pod Names        | Random          | Fixed       |
| DNS Names        | Dynamic         | Stable      |
| Scaling          | Parallel        | Sequential  |
| Identity         | No              | Yes         |
| Headless Service | Not Required    | Required    |
| Databases        | Not Recommended | Recommended |

---

# Real World Examples

## Deployment

Used for:

```text
Nginx

Apache

Spring Boot

React

Node.js
```

---

## StatefulSet

Used for:

```text
MySQL

Redis

MongoDB

PostgreSQL

Kafka

ZooKeeper
```

---

# Interview Questions

## Q1. Why does Redis use StatefulSet?

Because Redis requires:

* Stable hostname
* Stable DNS
* Persistent identity

---

## Q2. Why does Node.js use Deployment?

Because it is stateless.

Any Pod can serve requests.

---

## Q3. Why is clusterIP: None used?

To create a Headless Service.

```yaml
clusterIP: None
```

---

## Q4. What is the hostname of the first Redis Pod?

```text
redis-0
```

Full DNS:

```text
redis-0.redis.default.svc.cluster.local
```

---

## Q5. What happens if redis-0 is deleted?

Kubernetes recreates:

```text
redis-0
```

with the same hostname and identity.

---

# Summary

## Node.js

```text
Stateless Application
```

Uses:

```text
Deployment
```

---

## Redis

```text
Stateful Application
```

Uses:

```text
StatefulSet
```

---

## Communication

```text
Node.js
     |
     |
     v

redis-0.redis.default.svc.cluster.local

     |
     |
     v

Redis StatefulSet
```

---

# Final Architecture

```text
                    Deployment
                 (Node.js Pods)

                  node-app-abc
                  node-app-xyz

                         |
                         |
                         v

          redis-0.redis.default.svc.cluster.local

                         |
                         |
                         v

                 Headless Service

                         |
            -------------------------
            |                       |
            v                       v

        redis-0                 redis-1

      Stable DNS            Stable DNS

      Stateful Pod          Stateful Pod
```
-------------------------------------------------------------------------------------------
# Kubernetes RBAC (Role-Based Access Control) - Authentication and Authorization

## Introduction

In Kubernetes, security is handled using two major concepts:

1. Authentication → Who are you?
2. Authorization → What are you allowed to do?

Kubernetes uses **RBAC (Role-Based Access Control)** to control access to cluster resources.

RBAC helps administrators:

* Control user permissions
* Restrict access to namespaces
* Secure cluster resources
* Implement least-privilege access

---

# Authentication vs Authorization

## Authentication (Identity Verification)

Authentication answers:

```text
Who is the user?
```

Example:

```text
Username: dave
Certificate: dave.crt
Private Key: dave.key
```

Kubernetes verifies:

```text
Is this really Dave?
```

If verified:

```text
Authentication Successful
```

---

## Authorization (Permission Verification)

Authorization answers:

```text
What is Dave allowed to do?
```

Examples:

```text
Can Dave create Pods?
Can Dave delete Deployments?
Can Dave view Services?
```

These permissions are controlled using:

* Roles
* RoleBindings

---

# RBAC Architecture

```text
                User (Dave)
                       |
                       |
                       v
              Authentication
             (Certificate Check)
                       |
                       v
               Authentication
                  Successful
                       |
                       v
                  RoleBinding
                       |
                       v
                     Role
                       |
                       v
              Kubernetes Resources

        Pods | Services | Deployments
```

---

# Components of RBAC

## Role

A Role defines:

```text
What actions can be performed?
```

Examples:

* Create Pods
* Delete Deployments
* List Services

Think of a Role as:

```text
Permission Set
```

---

## RoleBinding

A RoleBinding defines:

```text
Who gets the permissions?
```

Example:

```text
Role = Developer Permissions

User = Dave

RoleBinding = Connects Dave to Developer Permissions
```

---

# Project Goal

We will:

1. Create a Namespace
2. Create a User (Dave)
3. Generate Certificates
4. Create a Role
5. Create a RoleBinding
6. Configure kubectl for Dave
7. Test access from another server

---

# Step 1: Create Namespace

On the Master Node:

```bash
# View namespaces
kubectl get namespaces

# Create namespace
kubectl create namespace dev
```

Verify:

```bash
kubectl get ns
```

Output:

```text
default
kube-system
dev
```

---

# Step 2: Create Working Directory

```bash
# Create directory
mkdir role

# Move into directory
cd role
```

---

# Step 3: Generate Private Key

Generate RSA private key for user Dave.

```bash
# Generate private key
openssl genrsa -out dave.key 2048
```

Generated file:

```text
dave.key
```

This file should be kept secure.

---

# Step 4: Generate Certificate Signing Request (CSR)

```bash
# Generate CSR
openssl req -new -key dave.key -out dave.csr
```

Enter details:

```text
Organization Name : namespace
Common Name (CN)  : dave
```

---

## Understanding Common Name (CN)

The Common Name becomes the Kubernetes username.

Example:

```text
CN = dave
```

Kubernetes identifies the user as:

```text
dave
```

---

# Step 5: Generate User Certificate

Use Kubernetes CA certificate to sign Dave's certificate.

```bash
# Generate user certificate
openssl x509 \
-req \
-in dave.csr \
-CA /etc/kubernetes/pki/ca.crt \
-CAkey /etc/kubernetes/pki/ca.key \
-CAcreateserial \
-out dave.crt \
-days 500
```

Generated file:

```text
dave.crt
```

---

# Authentication Flow

```text
             dave.key
                 |
                 |
                 v

             dave.crt
                 |
                 |
                 v

      Kubernetes CA Certificate
                 |
                 |
                 v

       Authentication Success
```

---

# Authorization

Now we define what Dave can do.

---

# Create Role

## Role YAML

```yaml
apiVersion: rbac.authorization.k8s.io/v1

# Kubernetes RBAC Role
kind: Role

metadata:

  # Namespace where role applies
  namespace: dev

  # Role name
  name: dave-role

rules:

# Allowed API groups
- apiGroups:

  - ""
  - apps

  # Allowed resources
  resources:

  - deployments
  - pods
  - services

  # Allowed actions
  verbs:

  - get
  - list
  - watch
  - create
  - update
  - patch
  - delete
```

---

## Understanding Role Permissions

### Resources

```yaml
resources:
- deployments
- pods
- services
```

Dave can access:

* Deployments
* Pods
* Services

---

### Verbs

```yaml
verbs:
- get
- list
- watch
- create
- update
- patch
- delete
```

Meaning:

| Verb   | Description          |
| ------ | -------------------- |
| get    | View single resource |
| list   | View all resources   |
| watch  | Monitor changes      |
| create | Create resource      |
| update | Modify resource      |
| patch  | Partial update       |
| delete | Remove resource      |

---

# Create Role

```bash
# Create role
kubectl apply -f role.yml
```

Verify:

```bash
kubectl get roles -n dev
```

---

# Create RoleBinding

RoleBinding connects:

```text
User → Role
```

---

## RoleBinding YAML

```yaml
apiVersion: rbac.authorization.k8s.io/v1

# Bind Role to User
kind: RoleBinding

metadata:

  # Binding name
  name: role-test

  namespace: dev

subjects:

# User receiving permissions
- kind: User

  # Username from certificate CN
  name: dave

  apiGroup: rbac.authorization.k8s.io

roleRef:

  # Reference a Role
  kind: Role

  # Role name
  name: dave-role

  apiGroup: rbac.authorization.k8s.io
```

---

# Create RoleBinding

```bash
# Create rolebinding
kubectl apply -f role-binding.yml
```

Verify:

```bash
kubectl get rolebindings -n dev
```

---

# RBAC Flow

```text
               Dave
                 |
                 |
                 v

          RoleBinding
                 |
                 |
                 v

            Dave Role
                 |
                 |
                 v

 Deployments | Pods | Services
```

---

# Configure User Credentials

Add Dave's certificate to kubeconfig.

```bash
# Add user credentials
kubectl config set-credentials dave \
--client-certificate=/root/role/dave.crt \
--client-key=/root/role/dave.key
```

---

# Create Context

A Context combines:

* Cluster
* User
* Namespace

```bash
# Create context
kubectl config set-context dave-context \
--cluster=kubernetes \
--namespace=dev \
--user=dave
```

---

# View Contexts

```bash
# List contexts
kubectl config get-contexts
```

Example:

```text
CURRENT   NAME
*         kubernetes-admin
          dave-context
```

---

# Worker Node Setup

## Copy Kubeconfig

On Master:

```bash
cat ~/.kube/config
```

Copy entire output.

---

## On Worker Node

Create file:

```bash
vim myconf
```

Paste copied configuration.

---

# Copy Certificates

Create directory:

```bash
mkdir role
cd role
```

Create:

```bash
vim dave.crt
vim dave.key
```

Paste contents from Master Node.

---

# Testing Access

## Get Pods

```bash
kubectl get pods \
--kubeconfig=myconf
```

---

## Create Deployment

```bash
kubectl create deployment test \
--image=httpd \
-n dev \
--kubeconfig=myconf
```

---

## Verify Deployment

```bash
kubectl get deployment \
--kubeconfig=myconf
```

---

## Verify Pods

```bash
kubectl get pods \
--kubeconfig=myconf
```

---

# What Can Dave Do?

Allowed:

```text
Create Pods
Delete Pods
View Pods

Create Services
Delete Services

Create Deployments
Delete Deployments
```

Inside Namespace:

```text
dev
```

---

# What Can Dave NOT Do?

Examples:

```text
Access kube-system namespace

Create Nodes

Manage Cluster Roles

Modify Control Plane
```

Because permissions are limited.

---

# View Kubeconfig

```bash
# Display kubeconfig
kubectl config \
--kubeconfig=myconf view
```

---

# View Contexts

```bash
kubectl config \
--kubeconfig=myconf get-contexts
```

---

# Switch Context

```bash
kubectl config \
--kubeconfig=myconf use-context dave-context
```

---

# Delete Context

```bash
kubectl config delete-context dave-context
```

---

# Authentication and Authorization Diagram

```text
                      Dave
                        |
                        |
             dave.crt + dave.key
                        |
                        |
                        v

                 Authentication
                        |
                        |
                        v

              Kubernetes API Server
                        |
                        |
                        v

                  RoleBinding
                        |
                        |
                        v

                     Role
                        |
                        |
                        v

           Pods / Services / Deployments
```

---

# Role vs RoleBinding

| Component   | Purpose              |
| ----------- | -------------------- |
| Role        | Defines permissions  |
| RoleBinding | Assigns permissions  |
| User        | Uses permissions     |
| Namespace   | Scope of permissions |

---

# Role vs ClusterRole

| Feature            | Role             | ClusterRole    |
| ------------------ | ---------------- | -------------- |
| Namespace Specific | Yes              | No             |
| Cluster Wide       | No               | Yes            |
| Used For           | Developers       | Administrators |
| Scope              | Single Namespace | Entire Cluster |

---

# Interview Questions

## Q1. What is RBAC?

RBAC stands for:

```text
Role Based Access Control
```

Used to control user permissions.

---

## Q2. Difference Between Authentication and Authorization?

### Authentication

```text
Who are you?
```

### Authorization

```text
What can you do?
```

---

## Q3. What is a Role?

A Role defines permissions inside a namespace.

---

## Q4. What is a RoleBinding?

A RoleBinding connects a user to a Role.

---

## Q5. What is the Common Name (CN) used for?

The CN becomes the Kubernetes username.

Example:

```text
CN=dave
```

User:

```text
dave
```

---

## Q6. Difference Between Role and ClusterRole?

Role:

```text
Namespace Scope
```

ClusterRole:

```text
Cluster Scope
```

---

# Summary

## Authentication

```text
User Identity Verification
```

Uses:

* Certificates
* Keys

---

## Authorization

```text
Permission Verification
```

Uses:

* Roles
* RoleBindings

---

## RBAC Workflow

```text
User
  |
  v

Authentication
  |
  v

RoleBinding
  |
  v

Role
  |
  v

Kubernetes Resources
```

---

## Key Point

```text
Authentication = Who Are You?

Authorization = What Are You Allowed To Do?
```


# Kubernetes ClusterRole and ClusterRoleBinding

## Introduction

In the previous RBAC example, we used:

* Role
* RoleBinding

These provide permissions only within a specific namespace.

Sometimes we need permissions across the entire Kubernetes cluster.

For example:

* Managing all namespaces
* Viewing all Pods
* Accessing the Kubernetes Dashboard
* Managing cluster-wide resources

For such cases, Kubernetes provides:

* ClusterRole
* ClusterRoleBinding

---

# Role vs ClusterRole

## Role

A Role provides permissions only inside a specific namespace.

Example:

```text
Namespace: dev

User Dave can:
✓ Create Pods
✓ Delete Pods
✓ View Services

Only inside dev namespace
```

---

## ClusterRole

A ClusterRole provides permissions across the entire cluster.

Example:

```text
All Namespaces

User can:
✓ View all Pods
✓ Create Deployments
✓ Manage Services
✓ Manage Nodes
✓ Manage Namespaces
```

---

# RoleBinding vs ClusterRoleBinding

## RoleBinding

Connects:

```text
User → Role
```

Scope:

```text
Single Namespace
```

---

## ClusterRoleBinding

Connects:

```text
User / ServiceAccount → ClusterRole
```

Scope:

```text
Entire Kubernetes Cluster
```

---

# RBAC Architecture

```text
                    User / ServiceAccount
                               |
                               |
                               v

                     ClusterRoleBinding
                               |
                               |
                               v

                        ClusterRole
                               |
                               |
                               v

                     Entire Kubernetes Cluster
```

---

# Why Kubernetes Dashboard Needs ClusterRole

The Kubernetes Dashboard displays:

* Pods
* Deployments
* Services
* Nodes
* ConfigMaps
* Secrets
* Namespaces
* StatefulSets
* DaemonSets

Since it must access resources across the cluster, it requires:

```text
ClusterRole
+
ClusterRoleBinding
```

---

# Kubernetes Dashboard Architecture

```text
                     Browser
                        |
                        |
                        v

             Kubernetes Dashboard
                        |
                        |
                        v

                ServiceAccount
                        |
                        |
                        v

               ClusterRoleBinding
                        |
                        |
                        v

                 cluster-admin
                        |
                        |
                        v

                Kubernetes API Server
                        |
                        |
                        v

              All Cluster Resources
```

---

# Step 1: Deploy Kubernetes Dashboard

Deploy Dashboard using:

```bash
# Deploy Kubernetes Dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

---

# Verify Dashboard Deployment

```bash
# View Dashboard resources
kubectl get all -n kubernetes-dashboard
```

Example Output:

```text
deployment.apps/kubernetes-dashboard
service/kubernetes-dashboard
pod/kubernetes-dashboard
```

---

# Step 2: Expose Dashboard Using NodePort

By default:

```text
Dashboard Service Type = ClusterIP
```

ClusterIP is accessible only inside the cluster.

We must change it to:

```text
NodePort
```

to access from a browser.

---

## Edit Dashboard Service

```bash
# Edit dashboard service
kubectl edit svc kubernetes-dashboard -n kubernetes-dashboard
```

---

## Before

```yaml
spec:
  type: ClusterIP
```

---

## After

```yaml
spec:
  # Expose dashboard outside cluster
  type: NodePort
```

Save and exit.

---

# Verify NodePort

```bash
# Verify service
kubectl get svc -n kubernetes-dashboard -o wide
```

Example:

```text
NAME                   TYPE       PORT(S)
kubernetes-dashboard   NodePort   443:30189/TCP
```

---

# Dashboard Access URL

Format:

```text
https://<NODE-IP>:<NODEPORT>
```

Example:

```text
https://35.239.229.82:30189
```

---

# Browser Warning

Since Dashboard uses a self-signed certificate:

```text
Privacy Warning
```

may appear.

Click:

```text
Advanced
   ↓
Accept Risk and Continue
```

---

# Service Accounts

## What is a ServiceAccount?

A ServiceAccount is a Kubernetes identity used by:

* Pods
* Applications
* Dashboard
* Automation Tools

Instead of human users.

Think of it as:

```text
User Account for Applications
```

---

# Create ServiceAccount

## serviceaccount.yml

```yaml
apiVersion: v1

# Kubernetes Service Account
kind: ServiceAccount

metadata:

  # ServiceAccount name
  name: sandry

  # Dashboard namespace
  namespace: kubernetes-dashboard
```

---

## Create ServiceAccount

```bash
# Create ServiceAccount
kubectl apply -f serviceaccount.yml
```

Verify:

```bash
kubectl get sa -n kubernetes-dashboard
```

---

# Why ServiceAccount Needs Permissions

Currently:

```text
sandry
```

has no permissions.

If it tries to access Dashboard:

```text
Access Denied
```

We need:

```text
ClusterRoleBinding
```

---

# ClusterRoleBinding

## Purpose

Connect:

```text
ServiceAccount
      ↓
ClusterRole
```

---

# clusterrole-sa.yml

```yaml
apiVersion: rbac.authorization.k8s.io/v1

# Cluster-wide binding
kind: ClusterRoleBinding

metadata:

  # Binding name
  name: admin-user

roleRef:

  # RBAC API group
  apiGroup: rbac.authorization.k8s.io

  # Bind to ClusterRole
  kind: ClusterRole

  # Built-in administrator role
  name: cluster-admin

subjects:

# ServiceAccount receiving permissions
- kind: ServiceAccount

  # ServiceAccount name
  name: sandry

  # Namespace of ServiceAccount
  namespace: kubernetes-dashboard
```

---

# Understanding cluster-admin

Kubernetes provides a built-in ClusterRole:

```text
cluster-admin
```

This role has:

```text
Full Cluster Permissions
```

Including:

* Pods
* Services
* Deployments
* Nodes
* Namespaces
* Secrets
* ConfigMaps
* Storage
* RBAC

Essentially:

```text
Administrator Access
```

---

# Create ClusterRoleBinding

```bash
# Create ClusterRoleBinding
kubectl apply -f clusterrole-sa.yml
```

Verify:

```bash
kubectl get clusterrolebinding
```

---

# RBAC Flow

```text
ServiceAccount (sandry)
           |
           |
           v

 ClusterRoleBinding
           |
           |
           v

    cluster-admin
           |
           |
           v

 Full Cluster Access
```

---

# Authentication Using Tokens

Dashboard login requires:

```text
Bearer Token
```

The token is stored inside a Secret.

---

# Create ServiceAccount Token Secret

## secret-sa.yml

```yaml
apiVersion: v1

# Secret resource
kind: Secret

# ServiceAccount token type
type: kubernetes.io/service-account-token

metadata:

  # Secret name
  name: mysecret-sa

  namespace: kubernetes-dashboard

  annotations:

    # Attach token to ServiceAccount
    kubernetes.io/service-account.name: sandry
```

---

# Create Secret

```bash
# Create secret
kubectl apply -f secret-sa.yml
```

---

# Verify Secret

```bash
# View secret
kubectl describe secret mysecret-sa \
-n kubernetes-dashboard
```

Output:

```text
Name: mysecret-sa

token:
eyJhbGciOiJSUzI1NiIs...
```

---

# Copy the Token

Copy:

```text
token:
xxxxxxxxxxxxxxxxxxxxxxxx
```

This is the Dashboard login token.

---

# Login to Dashboard

Open:

```text
https://<NODE-IP>:<NODEPORT>
```

Example:

```text
https://35.239.229.82:30189
```

Select:

```text
Token
```

Paste:

```text
Bearer Token
```

Click:

```text
Sign In
```

---

# Dashboard Login Flow

```text
             Browser
                |
                |
                v

        Dashboard Login Page
                |
                |
                v

           Token
                |
                |
                v

       ServiceAccount
          (sandry)
                |
                |
                v

      ClusterRoleBinding
                |
                |
                v

         cluster-admin
                |
                |
                v

      Kubernetes API Server
                |
                |
                v

      Dashboard Access Granted
```

---

# Verify Permissions

After login you can view:

```text
✓ Pods
✓ Services
✓ Deployments
✓ StatefulSets
✓ DaemonSets
✓ ConfigMaps
✓ Secrets
✓ Nodes
✓ Namespaces
✓ Persistent Volumes
✓ Storage Classes
```

---

# ClusterRole vs ClusterRoleBinding

| Component          | Purpose                          |
| ------------------ | -------------------------------- |
| ClusterRole        | Defines cluster-wide permissions |
| ClusterRoleBinding | Assigns those permissions        |
| ServiceAccount     | Identity used by Dashboard       |
| cluster-admin      | Full administrator access        |

---

# Role vs ClusterRole

| Feature          | Role             | ClusterRole        |
| ---------------- | ---------------- | ------------------ |
| Namespace Scoped | Yes              | No                 |
| Cluster Wide     | No               | Yes                |
| Used With        | RoleBinding      | ClusterRoleBinding |
| Access Scope     | Single Namespace | Entire Cluster     |

---

# Security Warning

The following configuration:

```yaml
roleRef:
  kind: ClusterRole
  name: cluster-admin
```

gives:

```text
Full Administrator Access
```

This is acceptable for:

* Labs
* Learning
* Development

Not recommended for:

* Production environments

Instead create custom ClusterRoles with limited permissions.

---

# Interview Questions

## Q1. What is a ClusterRole?

A ClusterRole defines permissions across the entire Kubernetes cluster.

---

## Q2. What is a ClusterRoleBinding?

A ClusterRoleBinding assigns a ClusterRole to a User, Group, or ServiceAccount.

---

## Q3. Why does Kubernetes Dashboard require ClusterRoleBinding?

Because it needs access to resources across multiple namespaces.

---

## Q4. What is cluster-admin?

A built-in ClusterRole that provides full administrator privileges.

---

## Q5. What is a ServiceAccount?

A Kubernetes identity used by applications and Pods instead of human users.

---

## Q6. How do we login to Kubernetes Dashboard?

Using:

```text
Bearer Token
```

generated from a ServiceAccount Secret.

---

# Summary

## ClusterRole

Defines:

```text
What actions are allowed?
```

Cluster-wide.

---

## ClusterRoleBinding

Defines:

```text
Who receives those permissions?
```

---

## ServiceAccount

Used by:

```text
Applications
Pods
Dashboard
Automation Tools
```

---

## Dashboard Authentication Flow

```text
ServiceAccount
      |
      v
ClusterRoleBinding
      |
      v
cluster-admin
      |
      v
Bearer Token
      |
      v
Dashboard Login
```

---

## Key Point

```text
Role            → Namespace Level Access

ClusterRole     → Cluster Level Access

RoleBinding     → Assign Role

ClusterRoleBinding → Assign ClusterRole
```

# Helm - Kubernetes Package Manager

## What is Helm?

Helm is the **package manager for Kubernetes**.

Just like:

* APT → Ubuntu packages
* YUM → RHEL packages
* NPM → NodeJS packages

Helm helps manage Kubernetes applications.

Helm automates:

* Creation
* Packaging
* Configuration
* Deployment
* Upgrades
* Rollbacks

of Kubernetes applications.

---

## Why Do We Need Helm?

In Kubernetes, applications are usually deployed using multiple YAML files:

```text
deployment.yaml
service.yaml
configmap.yaml
secret.yaml
ingress.yaml
persistentvolume.yaml
```

As applications grow:

* More microservices are added
* More YAML files are created
* Managing versions becomes difficult
* Updating configurations becomes complex

Without Helm:

```text
Application
   |
   +--> deployment.yaml
   +--> service.yaml
   +--> ingress.yaml
   +--> configmap.yaml
   +--> secret.yaml
```

Managing all these files manually becomes difficult.

---

## How Helm Solves This Problem

Helm combines all Kubernetes resources into a reusable package called a:

```text
Helm Chart
```

Instead of managing multiple YAML files:

```text
Application
    |
    v
Helm Chart
```

A single chart can deploy the entire application.

---

# Helm Architecture

```text
                Developer
                     |
                     |
                     v

                Helm Chart
                     |
                     |
                     v

                Helm Release
                     |
                     |
                     v

             Kubernetes Cluster
                     |
      ----------------------------------
      |              |                 |
      v              v                 v

 Deployment      Service         ConfigMap
```

---

# Important Helm Components

## Helm Chart

A Helm Chart is a package that contains:

* Deployments
* Services
* ConfigMaps
* Secrets
* Ingress
* PVCs

Everything required to run an application.

Think of it as:

```text
Application Template
```

---

## Helm Release

A running instance of a Helm Chart.

Example:

```bash
helm install myapp nginx-chart
```

Creates:

```text
Release Name = myapp
```

You can install the same chart multiple times.

Example:

```bash
helm install mysql-dev mysql-chart

helm install mysql-prod mysql-chart
```

Result:

```text
mysql-dev  --> Release 1

mysql-prod --> Release 2
```

---

## Helm Repository

A repository stores Helm Charts.

Examples:

* Bitnami Repository
* ArtifactHub
* Internal Company Repository

Think of it like:

```text
Docker Hub for Helm Charts
```

---

# Helm Workflow

```text
          Helm Repository
                  |
                  |
                  v

             Helm Chart
                  |
                  |
                  v

            helm install
                  |
                  |
                  v

             Helm Release
                  |
                  |
                  v

         Kubernetes Resources
```

---

# Installing Helm

## Download Helm

```bash
# Download Helm installation script
curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get-helm-3 > get_helm.sh
```

---

## Make Script Executable

```bash
# Give execute permission
chmod 700 get_helm.sh
```

---

## Install Helm

```bash
# Install Helm
./get_helm.sh
```

---

## Verify Installation

```bash
# Check Helm version
helm version
```

Example:

```text
version.BuildInfo{
Version:"v3.x.x"
}
```

---

# Creating a Helm Chart

## Create Chart

```bash
# Create Helm chart
helm create webapp
```

---

## Verify Chart Structure

```bash
ls -al webapp
```

Generated structure:

```text
webapp/

├── Chart.yaml
├── values.yaml
├── charts/
└── templates/
```

---

# Understanding Helm Chart Structure

## Chart.yaml

Contains chart metadata.

Example:

```yaml
# Chart API version
apiVersion: v2

# Chart name
name: webapp

# Chart description
description: Sample Helm Chart

# Chart version
version: 0.1.0

# Application version
appVersion: "1.0"
```

---

## values.yaml

Stores configurable values.

Example:

```yaml
# Number of replicas
replicaCount: 3

image:

  # Docker image repository
  repository: leaddevops/kubeserve

  # Docker image tag
  tag: "v2"

service:

  # Service type
  type: ClusterIP

  # Service port
  port: 80

  # Container port
  targetPort: 80
```

---

## templates/

Contains Kubernetes YAML templates.

Example:

```text
deployment.yaml
service.yaml
ingress.yaml
```

---

# Cleaning Default Templates

Move into templates directory:

```bash
cd webapp/templates
```

List files:

```bash
ls -al
```

Remove unwanted files:

```bash
# Remove default templates
rm -rf *.yaml NOTES.txt tests
```

---

# Creating Deployment Template

## deployment.yaml

```yaml
apiVersion: apps/v1

# Kubernetes Deployment
kind: Deployment

metadata:

  # Deployment name
  name: mydeploy-helm

spec:

  # Replica count from values.yaml
  replicas: {{ .Values.replicaCount }}

  selector:

    matchLabels:
      app: kubeserve

  template:

    metadata:

      labels:
        app: kubeserve

    spec:

      containers:

      - name: c1

        # Image from values.yaml
        image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
```

---

## Understanding Helm Variables

Helm uses Go Template syntax.

Example:

```yaml
{{ .Values.replicaCount }}
```

Reads:

```yaml
replicaCount: 3
```

from values.yaml

---

Example:

```yaml
{{ .Values.image.repository }}
```

Reads:

```yaml
repository: leaddevops/kubeserve
```

---

# Creating Service Template

## service.yaml

```yaml
apiVersion: v1

# Kubernetes Service
kind: Service

metadata:

  # Service name
  name: mysvc

spec:

  # Service type from values.yaml
  type: {{ .Values.service.type }}

  ports:

  - targetPort: {{ .Values.service.targetPort }}

    port: {{ .Values.service.port }}

  selector:

    app: kubeserve
```

---

# Deploying Helm Chart

Move outside chart directory:

```bash
cd
```

Install chart:

```bash
# Install chart
helm install release-chart-1 webapp
```

---

# Verify Resources

```bash
# View resources
kubectl get all
```

Expected:

```text
Deployment
Pods
Service
ReplicaSet
```

---

# Verify Helm Release

```bash
# List releases
helm list
```

Example:

```text
NAME              STATUS
release-chart-1   deployed
```

---

# Helm Release Concept

```text
Helm Chart
      |
      |
      v

release-chart-1

      |
      |
      v

Deployment
Service
Pods
```

---

# Uninstall Release

```bash
# Remove release
helm uninstall release-chart-1
```

This deletes all resources created by Helm.

---

# Helm Validation Commands

## Dry Run

Simulate deployment.

```bash
# Validate deployment
helm install webapp-release2 webapp --dry-run
```

---

## Render Templates

Generate YAML without deploying.

```bash
# Render YAML
helm template webapp-release2 webapp
```

---

## Lint Chart

Check chart syntax.

```bash
# Validate chart
helm lint webapp
```

---

# Helm Upgrade

Update an existing release.

```bash
# Upgrade release
helm upgrade myapp ./webapp
```

---

# Upgrade with Values File

```bash
# Upgrade using values file
helm upgrade myapp ./webapp \
-f values.yaml
```

---

# Override Values at Runtime

```bash
# Override image tag
helm upgrade myapp ./webapp \
--set image.tag=v3
```

---

# Show Default Values

```bash
# Display values.yaml
helm show values webapp
```

---

# Rollback Release

Suppose deployment fails after upgrade.

View history:

```bash
# Show revisions
helm history myapp
```

Example:

```text
REVISION
1
2
3
```

Rollback:

```bash
# Rollback to revision 2
helm rollback myapp 2
```

---

# Helm Commands Cheat Sheet

## Repository Commands

```bash
# Add repository
helm repo add <name> <url>

# Update repositories
helm repo update

# List repositories
helm repo list
```

---

## Installation Commands

```bash
# Install chart
helm install <release> <chart>

# Upgrade chart
helm upgrade <release> <chart>

# Install or upgrade
helm upgrade --install <release> <chart>
```

---

## Release Commands

```bash
# List releases
helm list

# Show status
helm status <release>

# Show release history
helm history <release>

# Remove release
helm uninstall <release>
```

---

## Debug Commands

```bash
# View rendered YAML
helm template

# Lint chart
helm lint

# Dry run
helm install --dry-run
```

---

# Helm Chart Lifecycle

```text
Create Chart
     |
     v

Edit values.yaml
     |
     v

Create Templates
     |
     v

helm lint
     |
     v

helm install
     |
     v

Release Created
     |
     v

helm upgrade
     |
     v

helm rollback
```

---

# GitHub Integration

Helm Charts should be stored in Git.

Example:

```bash
# Add files
git add .

# Commit changes
git commit -m "Added Helm Chart"

# Push to GitHub
git push origin master
```

Using Personal Access Token:

```text
Username : GitHub Username

Password : Personal Access Token
```

---

# Helm vs Kubernetes YAML

| Feature             | Plain YAML | Helm      |
| ------------------- | ---------- | --------- |
| Reusable            | No         | Yes       |
| Versioning          | Manual     | Built-in  |
| Rollback            | Difficult  | Easy      |
| Parameterization    | No         | Yes       |
| Environment Support | Limited    | Excellent |
| Upgrade Management  | Manual     | Automatic |

---

# Interview Questions

## Q1. What is Helm?

Helm is the package manager for Kubernetes.

---

## Q2. What is a Helm Chart?

A Helm Chart is a package containing Kubernetes resources.

---

## Q3. What is a Helm Release?

A deployed instance of a Helm Chart.

---

## Q4. What is values.yaml?

A file used to store configurable values.

---

## Q5. What command installs a Helm Chart?

```bash
helm install <release-name> <chart>
```

---

## Q6. What command upgrades a release?

```bash
helm upgrade <release-name> <chart>
```

---

## Q7. What command rolls back a release?

```bash
helm rollback <release-name> <revision>
```

---

# Summary

## Helm

```text
Package Manager for Kubernetes
```

---

## Helm Chart

```text
Application Package
```

---

## Helm Release

```text
Running Instance of a Chart
```

---

## values.yaml

```text
Stores Configurable Values
```

---

## Key Benefit

```text
Write Once

Deploy Anywhere

Manage Easily

Rollback Quickly
```

---

# Final Architecture

```text
                  Helm Repository
                          |
                          |
                          v

                     Helm Chart
                          |
                          |
                          v

                    Helm Release
                          |
        -----------------------------------
        |                |                |
        v                v                v

   Deployment        Service       ConfigMap

                          |
                          |
                          v

                Kubernetes Cluster
```

# Kubernetes Probes

## Introduction

Kubernetes provides a **Self-Healing** feature that automatically recovers failed containers and applications.

### What is Self-Healing?

A Pod's responsibility is:

* Start the container
* Monitor the container
* Ensure the container is always running

If a container exits or crashes:

```text
Container Crashed
        |
        v
      Pod
        |
        v
Container Restarted
```

Kubernetes automatically restarts the container based on the Pod's restart policy.

---

# Why Do We Need Probes?

Sometimes a container may be:

* Running but not responding
* Running but not ready to serve traffic
* Taking a long time to start

Example:

```text
Container Status = Running

But

Application Status = Not Working
```

Kubernetes cannot determine this by checking container status alone.

To solve this problem, Kubernetes uses:

```text
Probes
```

Probes check the health of applications running inside containers.

---

# Types of Probes

Kubernetes provides three types of probes:

1. Startup Probe
2. Liveness Probe
3. Readiness Probe

---

# Probe Architecture

```text
                 Kubernetes
                      |
                      |
                      v

              +----------------+
              |     Pod        |
              +----------------+
                      |
                      |
                      v

              +----------------+
              |   Container    |
              +----------------+
                      |
          -------------------------
          |           |           |
          v           v           v

     Startup     Liveness    Readiness
      Probe       Probe       Probe
```

---

# 1. Startup Probe

## What is a Startup Probe?

A Startup Probe checks whether the application has started successfully.

Some applications:

* Load huge configuration files
* Initialize databases
* Download dependencies
* Take several minutes to start

Example:

```text
Application Startup Time

Normal App  -> 5 Seconds

Legacy App  -> 2 Minutes
```

Without Startup Probe:

```text
Kubernetes thinks app is dead
```

and restarts it repeatedly.

---

## How Startup Probe Works

```text
Container Started
        |
        v

Startup Probe Runs
        |
        |
    Success?
    /      \
   Yes      No
    |        |
    v        v

Continue   Restart Container
```

---

## Important Points

### Startup Probe Runs Only Once

```text
Container Starts
       |
       v

Startup Probe

       |
       v

Success

       |
       v

Never Runs Again
```

---

## Use Cases

Use Startup Probe when:

* Spring Boot applications
* Java applications
* Legacy applications
* Applications with long startup time

---

# 2. Liveness Probe

## What is a Liveness Probe?

A Liveness Probe checks:

```text
Is the application still alive?
```

Even if the container is running, the application inside may be:

* Frozen
* Hung
* Deadlocked
* Unresponsive

---

## Example

Suppose:

```text
Nginx Container = Running

But

Nginx Service = Not Responding
```

Container Status:

```text
Running
```

Application Status:

```text
Dead
```

---

## How Liveness Probe Works

```text
Liveness Probe
       |
       v

Application Responding?
      /      \
    Yes       No
     |         |
     v         v

 Continue   Restart Container
```

---

## Key Purpose

Liveness Probe:

```text
Detect Dead Application
```

and

```text
Restart Container Automatically
```

---

## Important Points

If Liveness Probe fails:

```text
Container Terminated
        |
        v
New Container Created
```

---

## Common Use Cases

* Application hangs
* Memory deadlock
* Infinite loop
* Application crash

---

# 3. Readiness Probe

## What is a Readiness Probe?

Readiness Probe checks:

```text
Can this application serve requests?
```

---

## Real Example

A Spring Boot application starts.

However:

```text
Application Started

BUT

Database Connection Not Ready
```

If traffic is sent immediately:

```text
Application Returns Error
```

---

## How Readiness Probe Works

```text
Readiness Probe
       |
       v

Application Ready?
      /      \
    Yes       No
     |         |
     v         v

Receive      Remove Pod
Traffic      From Service
```

---

## Important Point

When Readiness Probe fails:

```text
Pod IS NOT Restarted
```

Instead:

```text
Pod Removed From Service Endpoints
```

Meaning:

```text
No Traffic Sent To Pod
```

---

## Readiness Flow

```text
Application Not Ready
          |
          v

Readiness Probe Failed
          |
          v

Service Removes Pod
          |
          v

No Traffic
```

---

# Liveness vs Readiness

| Feature                  | Liveness Probe     | Readiness Probe    |
| ------------------------ | ------------------ | ------------------ |
| Checks                   | Application Alive? | Application Ready? |
| Failure Action           | Restart Container  | Stop Traffic       |
| Container Restarted?     | Yes                | No                 |
| Service Traffic Allowed? | No                 | No                 |
| Runs Continuously?       | Yes                | Yes                |

---

# Startup vs Liveness vs Readiness

| Probe     | Purpose                  |
| --------- | ------------------------ |
| Startup   | Check startup completion |
| Liveness  | Check app health         |
| Readiness | Check traffic readiness  |

---

# Probes Demo

## Deployment YAML

File:

```bash
# Create deployment file
vim probesdemo.yml
```

---

## Deployment Configuration

```yaml
apiVersion: apps/v1

# Deployment object
kind: Deployment

metadata:

  # Deployment name
  name: probe-demo

spec:

  # Create one Pod
  replicas: 1

  selector:

    matchLabels:
      app: probe-demo

  template:

    metadata:

      labels:
        app: probe-demo

    spec:

      containers:

      - name: app

        # Nginx image
        image: nginx

        ports:

        # Application port
        - containerPort: 80

        #################################################
        # Startup Probe
        #################################################

        startupProbe:

          httpGet:

            # Check root URL
            path: /

            # Port to check
            port: 80

          # Allow 30 failures
          failureThreshold: 30

          # Check every 5 seconds
          periodSeconds: 5

        #################################################
        # Liveness Probe
        #################################################

        livenessProbe:

          httpGet:

            # Check application URL
            path: /

            port: 80

          # Start immediately
          initialDelaySeconds: 0

          # Check every 10 seconds
          periodSeconds: 10

          # Restart after 3 failures
          failureThreshold: 3

        #################################################
        # Readiness Probe
        #################################################

        readinessProbe:

          httpGet:

            # Check application URL
            path: /

            port: 80

          # Start immediately
          initialDelaySeconds: 0

          # Check every 5 seconds
          periodSeconds: 5

          # Mark unready after 3 failures
          failureThreshold: 3
```

---

# Service YAML

Create file:

```bash
# Create service file
vim service.yml
```

---

## Service Configuration

```yaml
apiVersion: v1

# Service object
kind: Service

metadata:

  # Service name
  name: service3

spec:

  # Expose application externally
  type: NodePort

  selector:

    # Select probe-demo Pod
    app: probe-demo

  ports:

  - targetPort: 80

    port: 80
```

---

# Deploy Resources

Create Deployment:

```bash
kubectl apply -f probesdemo.yml
```

Create Service:

```bash
kubectl apply -f service.yml
```

---

# Monitor Pod

Watch Pod status continuously:

```bash
watch kubectl get pods
```

Example:

```text
NAME                          READY   STATUS
probe-demo-abc123             1/1     Running
```

---

# Testing Liveness Probe

## Kill Main Process

Execute:

```bash
kubectl exec -it <pod-name> -- kill 1
```

---

## What Happens?

```text
Nginx Process Killed
         |
         v

Liveness Probe Fails
         |
         v

Container Restarted
```

---

## Verify

Watch:

```bash
watch kubectl get pods
```

You will notice:

```text
RESTARTS = 1
```

---

# Testing Readiness Probe

Enter Pod:

```bash
kubectl exec -it <pod-name> -- bash
```

---

## Remove Nginx Homepage

```bash
mv /usr/share/nginx/html/index.html /tmp
```

---

## What Happens?

Nginx now returns:

```text
404 Not Found
```

Probe Result:

```text
Readiness Probe Failed
```

---

## Kubernetes Response

```text
Readiness Probe Failed
          |
          v

Pod Removed From Service
          |
          v

No Traffic Sent
```

---

## Eventually

Liveness Probe also starts failing:

```text
Liveness Probe Failed
          |
          v

Container Restarted
```

---

# Complete Flow

```text
Application Running
          |
          |
          v

   Readiness Probe
          |
          |
      Success?
      /      \
    Yes       No
     |         |
     v         v

Receive     Remove Pod
Traffic     From Service

                 |
                 |
                 v

          Liveness Probe

                 |
                 |
             Success?
             /      \
           Yes       No
            |         |
            v         v

        Continue   Restart
                   Container
```

---

# Probe Parameters Explained

## initialDelaySeconds

Wait before starting the probe.

Example:

```yaml
initialDelaySeconds: 30
```

Meaning:

```text
Wait 30 seconds
Then start checking
```

---

## periodSeconds

How often probe runs.

Example:

```yaml
periodSeconds: 10
```

Meaning:

```text
Run every 10 seconds
```

---

## failureThreshold

Number of failures allowed.

Example:

```yaml
failureThreshold: 3
```

Meaning:

```text
Fail 3 Times

Then Take Action
```

---

# Real-World Example

## Startup Probe

```text
Spring Boot Application

Startup Time = 2 Minutes
```

Use:

```text
Startup Probe
```

---

## Readiness Probe

```text
Application Waiting For Database
```

Use:

```text
Readiness Probe
```

---

## Liveness Probe

```text
Application Frozen
```

Use:

```text
Liveness Probe
```

---

# Interview Questions

## Q1. Why are probes used?

To monitor application health inside containers.

---

## Q2. What happens when Liveness Probe fails?

```text
Container Restarted
```

---

## Q3. What happens when Readiness Probe fails?

```text
Pod Removed From Service
```

No traffic is sent.

---

## Q4. Does Readiness Probe restart containers?

```text
No
```

---

## Q5. Does Startup Probe run continuously?

```text
No
```

It runs only during startup.

---

## Q6. Which probe should be used for slow-starting applications?

```text
Startup Probe
```

---

# Summary

## Startup Probe

```text
Checks Startup Completion

Runs Once
```

---

## Readiness Probe

```text
Checks Traffic Readiness

Removes Pod From Service
```

---

## Liveness Probe

```text
Checks Application Health

Restarts Container
```

---

# Final Architecture Diagram

```text
                     Kubernetes
                          |
                          |
                          v

                     Pod/Container
                          |
      --------------------------------------
      |                  |                 |
      v                  v                 v

 Startup Probe     Readiness Probe    Liveness Probe

      |                  |                 |
      |                  |                 |
      v                  v                 v

 App Started?     Ready For Traffic?   App Alive?

      |                  |                 |
      |                  |                 |
      v                  v                 v

 Continue       Add/Remove From Service   Restart
                                         Container
```

# CI/CD Pipeline using GitHub, Maven, Docker, DockerHub, Jenkins and GKE

## Project Overview

This project demonstrates a complete CI/CD pipeline using:

* GitHub (Source Code Repository)
* Maven (Build Tool)
* Docker (Containerization)
* DockerHub (Image Registry)
* Jenkins (CI/CD Automation)
* Google Kubernetes Engine (Deployment Platform)

### Pipeline Flow

```text
Developer
    |
    v
GitHub Repository
    |
    v
Jenkins Pipeline
    |
    +--> Checkout Source Code
    +--> Build WAR Artifact
    +--> Build Docker Image
    +--> Push Image to DockerHub
    +--> Deploy to GKE
    |
    v
Running Application
```

---

# Architecture Diagram

```text
+-------------+
| Developer   |
+-------------+
       |
       v
+----------------------+
| GitHub Repository    |
+----------------------+
       |
       v
+----------------------+
| Jenkins Server       |
|                      |
| Checkout Code        |
| Maven Build          |
| Docker Build         |
| Docker Push          |
+----------------------+
       |
       v
+----------------------+
| DockerHub            |
+----------------------+
       |
       v
+----------------------+
| GKE Cluster          |
| Deployment           |
| Service              |
| Pods                 |
+----------------------+
```

---

# Step 1: Create Jenkins VM in GCP

Create an Ubuntu VM.

Recommended:

```text
OS           : Ubuntu 24.04
Machine Type : e2-medium
Disk         : 30 GB
```

Allow:

* HTTP
* HTTPS

---

# Install Java

```bash
# Update packages
sudo apt-get update

# Install Java 21
sudo apt install openjdk-21-jre-headless -y
```

Verify:

```bash
java -version
```

---

# Install Jenkins

## Add Jenkins Repository

```bash
# Create keyring directory
sudo mkdir -p /etc/apt/keyrings

# Download Jenkins key
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```

## Add Repository

```bash
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
```

## Install Jenkins

```bash
sudo apt update

sudo apt install jenkins -y
```

## Start Jenkins

```bash
sudo systemctl start jenkins

sudo systemctl enable jenkins
```

## Get Initial Password

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Access:

```text
http://<VM-PUBLIC-IP>:8080
```

---

# Install Docker

```bash
# Install Docker
sudo apt-get install docker.io -y
```

Start Docker:

```bash
sudo systemctl start docker

sudo systemctl enable docker
```

Verify:

```bash
docker version
```

---

# Allow Jenkins to Use Docker

```bash
# Add Jenkins user to Docker group
sudo usermod -aG docker jenkins

# Restart services
sudo systemctl restart docker
sudo systemctl restart jenkins
```

---

# Install kubectl

```bash
# Download installation script
sudo wget https://raw.githubusercontent.com/lerndevops/labs/master/scripts/installK8S.sh -P /tmp

# Install kubectl
sudo bash /tmp/installK8S.sh
```

Verify:

```bash
kubectl version --client
```

---

# Step 2: Configure Maven in Jenkins

Navigate:

```text
Manage Jenkins
  |
  +--> Tools
```

Add Maven:

```text
Name:
mymaven
```

Enable:

```text
Install Automatically
```

Save.

---

# Step 3: Configure DockerHub Credentials

Navigate:

```text
Manage Jenkins
   |
   +--> Credentials
```

Add:

```text
Kind:
Secret Text
```

Example:

```text
Credential ID:
DOCKERHUB_TOKEN
```

Paste DockerHub Access Token.

---

# Step 4: Create GKE Cluster

Navigate:

```text
Kubernetes Engine
   |
   +--> Create Cluster
```

Example:

```text
Cluster Name : cluster-1
Region       : us-central1
Nodes        : 2
```

---

# Step 5: Install GKE Plugin

Navigate:

```text
Manage Jenkins
   |
   +--> Plugins
```

Install:

```text
Google Kubernetes Engine Plugin
```

Restart Jenkins.

---

# Step 6: Create Service Account

Navigate:

```text
IAM & Admin
   |
   +--> Service Accounts
```

Create:

```text
jenkins-gke
```

Grant role:

```text
Kubernetes Engine Admin
```

---

# Generate JSON Key

```text
Service Account
    |
    +--> Keys
    |
    +--> Add Key
    |
    +--> JSON
```

Download key.

---

# Add GCP Credentials to Jenkins

Navigate:

```text
Manage Jenkins
    |
    +--> Credentials
```

Upload downloaded JSON.

Example:

```text
Credential ID:
ci-cd-gke
```

---

# Kubernetes Deployment Manifest

```yaml
# deployment.yml

apiVersion: apps/v1

# Deployment resource
kind: Deployment

metadata:

  # Deployment name
  name: addressbook

spec:

  # Number of pod replicas
  replicas: 2

  selector:
    matchLabels:
      app: addressbook

  template:

    metadata:
      labels:
        app: addressbook

    spec:

      containers:

      - name: addressbook

        # DockerHub image
        image: YOUR_DOCKERHUB_USERNAME/myappimage01

        ports:

        # Application port
        - containerPort: 8080

---

apiVersion: v1

# Service resource
kind: Service

metadata:

  # Service name
  name: addressbook-service

spec:

  # Expose application externally
  type: LoadBalancer

  selector:
    app: addressbook

  ports:
  - port: 80
    targetPort: 8080
```

---

# Complete Jenkins Pipeline

```groovy
pipeline {

    agent any

    tools {
        maven 'mymaven'
    }

    environment {

        # GCP Project ID
        PROJECT_ID = 'YOUR_PROJECT_ID'

        # GKE Cluster Name
        CLUSTER_NAME = 'cluster-1'

        # Cluster Zone
        LOCATION = 'us-central1-a'

        # Jenkins Credential ID
        CREDENTIALS_ID = 'ci-cd-gke'
    }

    stages {

        stage('Checkout Code') {

            steps {

                # Clone source code
                git 'https://github.com/Sonal0409/DevOpsCodeDemo.git'
            }
        }

        stage('Build Artifact') {

            steps {

                # Build WAR file
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {

            steps {

                # Build Docker image
                sh 'docker build -t myappimage01 .'
            }
        }

        stage('Push Image To DockerHub') {

            steps {

                withCredentials([
                    string(
                        credentialsId: 'DOCKERHUB_TOKEN',
                        variable: 'DOCKERHUB_TOKEN'
                    )
                ]) {

                    # Login to DockerHub
                    sh 'docker login -u YOUR_DOCKERHUB_USERNAME -p ${DOCKERHUB_TOKEN}'
                }

                # Tag image
                sh 'docker tag myappimage01 YOUR_DOCKERHUB_USERNAME/myappimage01'

                # Push image
                sh 'docker push YOUR_DOCKERHUB_USERNAME/myappimage01'
            }
        }

        stage('Deploy To GKE') {

            steps {

                step([
                    $class: 'KubernetesEngineBuilder',

                    # GCP Project
                    projectId: env.PROJECT_ID,

                    # Cluster Name
                    clusterName: env.CLUSTER_NAME,

                    # Cluster Zone
                    location: env.LOCATION,

                    # Deployment Manifest
                    manifestPattern: 'deployment.yml',

                    # Jenkins GCP Credentials
                    credentialsId: env.CREDENTIALS_ID,

                    # Verify deployment
                    verifyDeployments: true
                ])
            }
        }
    }
}
```

---

# Verification Commands

```bash
# Check deployments
kubectl get deployment

# Check pods
kubectl get pods

# Check services
kubectl get svc
```

---

# Interview Questions

## What is CI?

Continuous Integration.

Automatically builds and tests code whenever changes are pushed.

## What is CD?

Continuous Delivery / Continuous Deployment.

Automatically deploys applications.

## Why DockerHub?

Stores container images.

## Why Maven?

Builds Java applications and generates WAR/JAR files.

## Why Jenkins?

Automates the complete CI/CD workflow.

## Why GKE?

Managed Kubernetes service provided by Google Cloud.
