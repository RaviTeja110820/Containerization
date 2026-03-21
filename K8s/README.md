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

✔ Manager vs Worker (Swarm vs K8s difference)
✔ API Server flow and scheduling
✔ ETCD stores cluster metadata
✔ Kubelet pulls images and creates pods
✔ CNI provides networking (VXLAN tunnels)
✔ Replicas handled by controller
✔ Pods created across worker nodes
✔ Cluster state always maintained

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

---



