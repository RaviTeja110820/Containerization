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
