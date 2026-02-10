# Containers & Docker 
------------------------------------------------------------------------

## 🧠 1) Containerization (Core Idea)

Containerization is a method of packaging an application with: - Code -
Runtime - Libraries - Dependencies - Config

so it can run **anywhere consistently**.

> "Works on my machine" problem is solved.


Before containers:

-   App works in Dev
-   Fails in Test
-   Behaves differently in Prod

Because OS, libraries, runtime versions, and configs change.

### ✅ Solution

Package everything together:

    Application
    + Runtime
    + Libraries
    + Dependencies
    + Config
    -------------------
    = Portable Unit

Run anywhere → same behavior.

------------------------------------------------------------------------

## 📦 What is a Container?

A **container** is a running instance of an image.

It is: - Lightweight - Fast to start - Isolated - Shares host OS kernel



## 🖥️ Host Server

The machine where containers run.

Examples: - Developer laptop - Cloud VM - Physical server - Kubernetes
node

### Host provides

-   CPU
-   Memory
-   Disk
-   Network
-   OS Kernel

👉 Containers **share the kernel** → lightweight.

------------------------------------------------------------------------

## ⚙️ Container Runtime Engine

Software responsible for executing containers. (or) Software that runs containers.

Examples: - Docker Engine - containerd - CRI-O

### Runtime responsibilities    

-   Pull images
-   Create container filesystem
-   Allocate CPU/RAM
-   Configure networking
-   Start / stop process

------------------------------------------------------------------------

## 📦 Image (Blueprint)

An image is a **read-only template** used to create containers. (or) An **image** is a blueprint/template used to create containers.

Contains: - OS layer - Packages - Application - Start command

### Important

Container = Image + Running state

------------------------------------------------------------------------

## 🧱 Image Layers (Very Important)

Docker builds images in layers.

Example:

    FROM ubuntu
    RUN apt install java
    COPY app.jar

Layers: 1. Ubuntu 2. Java 3. App

### Benefit

If app changes → only last layer rebuilds.

------------------------------------------------------------------------

## 🧱 Base Image

Starting foundation.

Examples: - ubuntu - alpine - node - python - openjdk

------------------------------------------------------------------------

## 🧱 Custom Image

Your application added on top of base image. (or) Your own image built on top of base image.

Example: - Base: ubuntu - Add: Java - Add: your app

Now it becomes your custom image.

Example:

    node:18 + your app

------------------------------------------------------------------------

## ▶️ Container

A running instance of an image.

You can run multiple containers from one image.

    docker run nginx
    docker run nginx

Two containers → same image.

### Each container has

-   Own IP
-   Own processes
-   Own logs

------------------------------------------------------------------------

## 🐳 Docker

Platform/tool to: - Build images - Run containers - Manage lifecycle -
Push/Pull images

------------------------------------------------------------------------

## 🌍 Registry

Storage for container images.

Like: - GitHub → code - Registry → images

Types: - Public - Private

------------------------------------------------------------------------

## 🚢 Docker Hub

Default public registry by Docker.

Example:

    docker pull nginx

------------------------------------------------------------------------

## 🔁 End-to-End Lifecycle

    Developer writes code
            ↓
    Dockerfile created
            ↓
    docker build
            ↓
    Image created
            ↓
    docker push
            ↓
    Registry
            ↓
    Server pulls image
            ↓
    Runtime starts container
            ↓
    App running

------------------------------------------------------------------------
## 🔁 Runtime View Diagram
```

    +---------------------------------------+
    |              Host Server              |
    |                                       |
    |  +---------------------------------+  |
    |  | Container Runtime (Docker)      |  |
    |  |                                 |  |
    |  |   +-----------+  +-----------+  |  |
    |  |   |Container1 |  |Container2 |  |  |
    |  |   +-----------+  +-----------+  |  |
    |  +---------------------------------+  |
    |                                       |
    +---------------------------------------+
```

## 🧠 What runs inside container?

Only **one main process**.

If it stops → container exits.

------------------------------------------------------------------------

## ⚡ Containers vs Virtual Machines

  Feature       VM        Container
  ------------- --------- -----------
  OS per app    Yes       No
  Size          GB        MB
  Boot          Minutes   Seconds
  Performance   Heavy     Fast

------------------------------------------------------------------------

## 🔥 Resource Sharing

Shared: - Kernel - Drivers

Isolated: - Filesystem - Network - Processes

------------------------------------------------------------------------

## 🎯 Interview Quick Definitions

-   **Container** → Running instance of an image.
-   **Image** → Blueprint/template.
-   **Base image** → Starting layer.
-   **Custom image** → Modified base with app.
-   **Docker** → Tool/platform.
-   **Registry** → Storage for images.
-   **Docker Hub** → Public registry.
-   **Runtime** → Executes containers.
-   **Host** → Machine where containers run.

------------------------------------------------------------------------

## 🚀 Memory Trick

👉 Image is like **class**\
👉 Container is like **object**

Many containers can be created from one image.


# Docker Installation & Runtime Notes (with Corrections and Comments)

This document covers:

-   Installing Docker on Ubuntu
-   Pulling & managing images
-   Running containers
-   Foreground vs Detached modes
-   Exec / attach
-   Cleanup commands

Extra explanations are added using `#` comments where needed.

------------------------------------------------------------------------

## 🐳 INSTALL DOCKER (Ubuntu)

### Add Docker's official GPG key

``` bash
sudo apt update
sudo apt install ca-certificates curl -y

# Create directory for keyrings
sudo install -m 0755 -d /etc/apt/keyrings

# Download Docker GPG key
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc

# Give read permission
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

------------------------------------------------------------------------

## Add the repository to Apt sources

``` bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

# Update package index after adding repo
sudo apt update
```

------------------------------------------------------------------------

## Install Docker packages

``` bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

------------------------------------------------------------------------

## Verify installation

``` bash
docker --version   # Should print installed docker version
```

------------------------------------------------------------------------

# 📦 IMAGES in DOCKER

## Pull a Base Image

``` bash
docker pull ubuntu          # Pull latest ubuntu
docker pull ubuntu:22.04    # Pull specific version
```

------------------------------------------------------------------------

## List images

``` bash
docker images
```

------------------------------------------------------------------------

## Remove image

``` bash
docker rmi ubuntu
docker rmi ubuntu:22.04
```

⚠️ Image must not be used by any container.

------------------------------------------------------------------------

# 🚀 Docker Containers -- Runtime Options

Important: 👉 Container is created **from an image**\
👉 Then container must be **running**

------------------------------------------------------------------------

## 1️⃣ Foreground mode (-it)

    -i = interactive
    -t = terminal

Meaning: - Container created - Container running - You get shell access

``` bash
docker run -it ubuntu
```

------------------------------------------------------------------------

## List containers

``` bash
docker ps -a   # show running + stopped
```

------------------------------------------------------------------------

### Come out of container but KEEP running

Press:
```
    CTRL + p + q
```
(Not pq, press p then q)

------------------------------------------------------------------------

### Go inside again

``` bash
docker attach <containerid>
```

------------------------------------------------------------------------

### Stop container from inside

``` bash
exit
```

------------------------------------------------------------------------

## 2️⃣ Detached mode (-d)

Meaning: - Container created - Running in background - No terminal
attached

``` bash
docker run -d nginx
```

------------------------------------------------------------------------

## List containers

``` bash
docker ps -a
```

------------------------------------------------------------------------

## Docker system information

``` bash
docker info
```

------------------------------------------------------------------------

## 3️⃣ Execute a command inside running container

Instead of attach, better use exec.

``` bash
docker exec -it <container_id> bash
```

- opens new shell session

- container main process keeps running

## 4️⃣ Delete all containers

    docker rm -f $(docker ps -aq)

- -f → force stop + delete

- aq → all container ids




## 5️⃣ Clean Docker host

    docker system prune --all


It will: - Remove stopped containers - Remove unused networks - Remove
unused images - Remove build cache

System will ask → type `y`

------------------------------------------------------------------------

## 🧠 Important Interview Corrections

✔️ `CTRL + p + q` → detach, keep running\
✔️ `exit` → stop container\
✔️ `exec` → open new session\
✔️ Cannot delete image if container exists\
✔️ Detached mode is used mostly in servers

------------------------------------------------------------------------

## 🎯 Memory Trick

`run` → create + start\
`start` → start existing\
`exec` → go inside\
`rm` → delete


# Port Mapping / Port Forwarding in Docker (Detailed Notes)

This document explains in depth:

-   Why port mapping is needed
-   Container port vs host port
-   Syntax and flags
-   What happens internally
-   Common mistakes
-   Interview tips

------------------------------------------------------------------------

## 🧠 Core Problem

Inside a container, an application runs on some port.

Example: - Nginx → 80 - Spring Boot → 8080 - Node → 3000

👉 That port is available **inside the container network only**.

External users (browser / internet / other machines) **cannot access it
directly**.

------------------------------------------------------------------------

## 🎯 Why?

Because Docker creates its own:

-   Network namespace
-   Private IP
-   Internal bridge network

So container port ≠ host port.

------------------------------------------------------------------------

## ✅ Solution → Port Mapping / Forwarding

We map:

    HOST PORT  →  CONTAINER PORT

Example:

    8989:80

Meaning:

    Request comes to VM port 8989
            ↓
    Docker forwards to container port 80

------------------------------------------------------------------------

## 🔁 Visual Diagram

    Browser
       |
       |  http://server-ip:8989
       v
    +---------------------------+
    |        Host Server        |
    |                           |
    |   Port 8989               |
    |      ↓                    |
    |   Docker Engine           |
    |      ↓                    |
    |   Container : Port 80     |
    +---------------------------+

------------------------------------------------------------------------

## 🧱 Important Rule

❗ If container is already created →\
you **CANNOT** change or add port mapping.

You must delete and recreate.

------------------------------------------------------------------------

## ⚙️ Port mapping happens at

    docker run

time only.

------------------------------------------------------------------------

## 🚩 Flag for Port Mapping

### `-p` → manual mapping

### `-P` → automatic mapping

------------------------------------------------------------------------

# 1️⃣ Manual Port Mapping (-p)

Syntax:

    -p hostPort:containerPort

------------------------------------------------------------------------

## Example

``` bash
docker run --name web1 -d -p 8989:80 nginx
```

### Meaning

-   Container runs nginx
-   Nginx listens on port 80
-   Host exposes port 8989
-   Access via:

```{=html}
<!-- -->
```
    http://<server-ip>:8989

------------------------------------------------------------------------

## Multiple port mapping example

``` bash
docker run -p 8080:80 -p 8443:443 nginx
```

------------------------------------------------------------------------

------------------------------------------------------------------------

# 2️⃣ Automatic Port Mapping (-P)

Docker will automatically assign a **random free port** on host.

------------------------------------------------------------------------

## Example

``` bash
docker run --name web2 -d -P httpd
```

------------------------------------------------------------------------

Now check mapping:

``` bash
docker ps
```

Output example:

    0.0.0.0:32768->80/tcp

Meaning: Host 32768 → container 80

------------------------------------------------------------------------

## When do we use -P?

Mostly in: - Testing - Temporary environments

Not ideal for production.

------------------------------------------------------------------------

## 🧠 How traffic flows internally

1.  Request hits host port
2.  Docker NAT rules redirect
3.  Packet goes to container IP
4.  App responds

------------------------------------------------------------------------

## 🧪 Real-time Example (Step-by-step)

### Run container

``` bash
docker run -d -p 5000:80 nginx
```

### Open browser

    http://server-ip:5000

You will see nginx page.

------------------------------------------------------------------------

## 🔍 How to check port mapping

``` bash
docker ps
```

or

``` bash
docker port <container_id>
```

------------------------------------------------------------------------

## ❌ Common Beginner Mistakes

❌ Forget mapping → site not accessible\
❌ Using wrong host port\
❌ Firewall blocking host port\
❌ Trying to change mapping after creation

------------------------------------------------------------------------

## 🔥 Production Best Practices

✔️ Use known ports\
✔️ Document mappings\
✔️ Avoid random ports\
✔️ Use reverse proxy when many containers

------------------------------------------------------------------------

## 🎯 Interview Questions They Ask

### Q: Why port mapping needed?

Because containers run in isolated network.

### Q: Can we change mapping later?

No → recreate container.

### Q: Difference between -p and -P?

-p manual, -P automatic.

------------------------------------------------------------------------

## 🧠 Memory Trick

Container = inside house\
Port mapping = main gate

Without gate → nobody can enter.

------------------------------------------------------------------------

## 🚀 Summary

-   Container ports are private.
-   Host ports are public.
-   `-p` creates connection between them.
-   Done during `docker run`.



# Editing Data Inside an Nginx Container -- What Really Happens

This note explains an important Docker concept:

👉 What happens when we change files **inside a running container**.

------------------------------------------------------------------------

## 🧪 What was done in the example

### Step 1 --- Run nginx container with port mapping

``` bash
docker run -d --name web -p 8787:80 nginx
```

### Meaning

-   Container name → `web`
-   Host port → `8787`
-   Container port → `80`
-   nginx runs in background

Access in browser:

    http://server-ip:8787

------------------------------------------------------------------------

## Step 2 --- Enter container

``` bash
docker exec -it web /bin/bash
```

Now you are inside the container OS.

------------------------------------------------------------------------

## Step 3 --- Go to nginx HTML folder

``` bash
cd /usr/share/nginx/html
```

This is default nginx website directory.

------------------------------------------------------------------------

## Step 4 --- List files

``` bash
ls
```

You will see:

    50x.html
    index.html

------------------------------------------------------------------------

## Step 5 --- View or edit file

``` bash
cat index.html
```

Usually edited with:

``` bash
vi index.html
```

------------------------------------------------------------------------

## 🎯 What happens after editing?

Refresh browser:

    http://server-ip:8787

👉 You will see updated content.

Because nginx serves files from this directory.

------------------------------------------------------------------------

# 🚨 MOST IMPORTANT PART

This change is **TEMPORARY**.

------------------------------------------------------------------------

## ❓ Why temporary?

Container filesystem is **ephemeral**.

If container is: - Deleted - Recreated - Replaced

👉 All changes are LOST.

------------------------------------------------------------------------

## 🧪 Proof

``` bash
docker rm -f web
docker run -d --name web -p 8787:80 nginx
```

Refresh browser → default page returns.

------------------------------------------------------------------------

## 🧠 Why this happens

You changed:

    Container

But not:

    Image

New container → new fresh copy from image.

------------------------------------------------------------------------

# 📦 Image vs Container

  Image       Container
  ----------- --------------
  Blueprint   Running copy
  Permanent   Temporary
  Reusable    Disposable

------------------------------------------------------------------------

# 🏢 Real DevOps / Production Approach

We NEVER modify container manually.

Instead:

    Dockerfile
       ↓
    COPY website files
       ↓
    Build new image
       ↓
    Run container

------------------------------------------------------------------------

## Example Dockerfile

``` dockerfile
FROM nginx
COPY index.html /usr/share/nginx/html/index.html
```

Build image:

``` bash
docker build -t mynginx .
```

Run:

``` bash
docker run -p 8787:80 mynginx
```

Now every new container will have your updated site.

------------------------------------------------------------------------

## ❌ Why manual editing is bad

-   Not repeatable
-   Lost after restart
-   Not version controlled
-   Cannot scale
-   Breaks CI/CD

------------------------------------------------------------------------

## ✅ When manual edit is OK

Only for: - Learning - Debugging - Quick experiments

------------------------------------------------------------------------

## 🎯 Interview Answer (Golden Line)

If asked:

**"What happens if you change file inside container?"**

Answer:

> It works immediately, but changes are temporary. If container is
> removed, data is lost. Proper method is rebuilding image or using
> volumes.

------------------------------------------------------------------------

## 🚀 Bonus Knowledge

To make data permanent, we use:

1.  Build new image
2.  Docker volumes
3.  Bind mounts

------------------------------------------------------------------------

## 🧠 Memory Trick

Image = Original book\
Container = Photocopy

Write on photocopy → original never changes.
