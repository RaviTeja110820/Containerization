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

    CTRL + p + q

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

# opens new shell session

# container main process keeps running


    ---

    ## 4️⃣ Delete all containers

    ```bash
    docker rm -f $(docker ps -aq)

# -f → force stop + delete

# aq → all container ids


    ---

    ## 5️⃣ Clean Docker host

    ```bash
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
