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
```
  Feature       VM        Container
  ------------- --------- -----------
  OS per app    Yes       No
  Size          GB        MB
  Boot          Minutes   Seconds
  Performance   Heavy     Fast
```
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


# Docker Volumes

Docker volumes are the **preferred way to persist data** generated and
used by containers.

By default, when a container is removed, its data is also removed.
Volumes help keep the data safe even after the container is deleted.

------------------------------------------------------------------------

## Why Volumes Are Important

✅ Data persistence\
✅ Easy backup & migration\
✅ Share data between containers\
✅ Better performance than container writable layer\
✅ Managed by Docker

------------------------------------------------------------------------

# Named Volumes

A named volume is created and managed by Docker and referenced by a
name.

Example name:

    myvol

------------------------------------------------------------------------

## Use Case 1

**Preserve container data on the Docker host even if the container is
deleted.**

------------------------------------------------------------------------

## Step 1 -- Create a Volume

``` bash
docker volume create myvol
```

### What happens internally?

Docker creates a directory inside:

    /var/lib/docker/volumes/

------------------------------------------------------------------------

## Step 2 -- List Volumes

``` bash
docker volume ls
```

------------------------------------------------------------------------

## Step 3 -- Inspect Volume Details

``` bash
docker volume inspect myvol
```

This shows: - Mountpoint (actual host path) - Driver - Labels - Scope

>> docker inspect is used to see low-level details of a container (network, IP, mounts, ports, env vars, etc ```docker inspect <container_id_or_name>```

------------------------------------------------------------------------

### Quick way to get only the path

``` bash
docker volume inspect -f '{{.Mountpoint}}' myvol
```

Example output:

    /var/lib/docker/volumes/myvol/_data

------------------------------------------------------------------------

## Step 4 -- Mount Volume to a Container

``` bash
docker run -it -v myvol:/tmp ubuntu
```

### Meaning:

    myvol  -> volume name on host
    /tmp   -> directory inside container

Now whatever is written to `/tmp` will be stored in the volume.

------------------------------------------------------------------------

## Step 5 -- Create Files Inside Container

``` bash
cd /tmp
touch contfile1 contfile2
ls
```

Files are created.

------------------------------------------------------------------------

## Step 6 -- Exit the Container

``` bash
exit
```

------------------------------------------------------------------------

## Step 7 -- Check Data on Host

Go to volume directory:

``` bash
cd /var/lib/docker/volumes/myvol/_data
ls
```

You will see:

    contfile1
    contfile2

🔥 This proves data is stored outside the container.

------------------------------------------------------------------------

## Step 8 -- Delete the Container

``` bash
docker rm -f <container_id>
```

------------------------------------------------------------------------

## Step 9 -- Verify Data Again

``` bash
cd /var/lib/docker/volumes/myvol/_data
ls
```

✅ Files are STILL there.\
Volume keeps the data safe.

------------------------------------------------------------------------

# Important Interview Points ⭐

-   Volumes are **not deleted** when a container is removed.
-   Must delete volume manually.
-   Best for databases, logs, application uploads.
-   Multiple containers can use the same volume.

------------------------------------------------------------------------

## Delete a Volume

⚠️ Container must not be using it.

``` bash
docker volume rm myvol
```

------------------------------------------------------------------------

## Remove Unused Volumes

``` bash
docker volume prune
```

------------------------------------------------------------------------

# Real DevOps Example 🔥

Example: - MySQL container stores DB files in a volume. - Container
crashes or removed. - New container can reuse the same volume. - No data
loss.

------------------------------------------------------------------------

# Volume vs Bind Mount (Quick Idea)

Volume → Managed by Docker\
Bind Mount → Uses host directory directly

Example bind mount:

``` bash
docker run -v /host/path:/container/path
```

------------------------------------------------------------------------



# Summary

✔ Containers are temporary\
✔ Volumes make data permanent\
✔ Stored under `/var/lib/docker/volumes/`\
✔ Survive container deletion

    No volume = No data after container removal


# Docker Shared Volume Flow (Ubuntu → Volume → Nginx → Browser)

This note explains how two containers share the same data using a
**named volume**.
```
Flow:

    Ubuntu container  --->  Docker Volume  --->  Nginx container  --->  Browser
    (write file)            (stores data)        (serves file)          (view page)
```
------------------------------------------------------------------------

## Goal of This Setup

-   Ubuntu container creates `index.html`
-   File is stored in Docker volume
-   Nginx container reads the same file
-   Browser accesses it using host IP + port

Data is shared **without copying** between containers.

------------------------------------------------------------------------

## Step 1 -- Create a Named Volume

``` bash
docker volume create myvol
```

Verify:

``` bash
docker volume ls
```

------------------------------------------------------------------------

## Step 2 -- Run Ubuntu Container & Attach Volume

Mount volume to `/tmp` inside Ubuntu.

``` bash
docker run -it --name ubuntu1 -v myvol:/tmp ubuntu bash
```

------------------------------------------------------------------------

## Step 3 -- Create index.html inside Ubuntu

``` bash
cd /tmp
date >> index.html
cat index.html
```

What happens:

-   File is written to `/tmp`
-   `/tmp` is connected to volume
-   Data is actually stored in:

```{=html}
<!-- -->
```
    /var/lib/docker/volumes/myvol/_data

Exit container:

``` bash
exit
```

------------------------------------------------------------------------

## Step 4 -- Verify Data on Docker Host (Optional)

``` bash
cd /var/lib/docker/volumes/myvol/_data
ls
```

You should see:

    index.html

------------------------------------------------------------------------

## Step 5 -- Run Nginx Container Using SAME Volume

Now mount the volume to Nginx default website directory.

``` bash
docker run -d --name nginx1 -p 8989:80 -v myvol:/usr/share/nginx/html nginx
```

### Meaning:

    myvol -> shared storage
    /usr/share/nginx/html -> where nginx reads web files
    8989 -> host port
    80   -> container port

------------------------------------------------------------------------

## Step 6 -- Access from Browser

Open:

    http://<EC2-IP>:8989

You will see the content written from Ubuntu container.

🔥 Ubuntu writes → Nginx reads → Browser shows.

------------------------------------------------------------------------

## Step 7 -- Prove Real-Time Sharing

Run Ubuntu again:

``` bash
docker start -ai ubuntu1
```

Append more data:

``` bash
date >> /tmp/index.html
exit
```

Refresh browser.

✅ New content appears.

------------------------------------------------------------------------

## Important Understanding ⭐

### Containers are isolated

They normally cannot see each other's files.

### Volume is the bridge

Both containers mount the same storage.

### Data location

    /var/lib/docker/volumes/myvol/_data

### Even if Ubuntu container is deleted

Data remains.

------------------------------------------------------------------------

## Remove Containers (Data Still Safe)

``` bash
docker rm -f ubuntu1
docker rm -f nginx1
```

Volume still exists.

------------------------------------------------------------------------

## Remove Volume (Data Gone)

``` bash
docker volume rm myvol
```

------------------------------------------------------------------------

## Real DevOps / Interview Explanation 🔥

This pattern is used in:

-   App container writing logs
-   Nginx serving uploads
-   Multiple microservices sharing files
-   CI/CD artifact sharing

------------------------------------------------------------------------

## One-Line Memory Trick 🧠

    Volume = Common Hard Disk for Containers

------------------------------------------------------------------------


# Docker Bind Mount Volume

In a **bind mount**, any existing directory from the Docker host
(VM/server) is mounted directly into a container.

Unlike named volumes, Docker does not manage the data location. You
choose the path.

------------------------------------------------------------------------

## When Do We Use Bind Mounts?

Common in development & CI/CD environments.

✅ Developers update code\
✅ Host machine gets latest files\
✅ Container instantly sees changes\
✅ No need to rebuild image every time

------------------------------------------------------------------------

## Real Scenario

Developers maintain the application in a Git repository.

Example repository:

    https://github.com/Sonal0409/ecomm.git

Goal:

-   Download code to Docker host
-   Run an Apache (httpd) container
-   Mount the code into the web server directory
-   Access application from browser

------------------------------------------------------------------------

## Architecture Flow

    Git Repo → Docker Host Folder → Bind Mount → httpd Container → Browser

------------------------------------------------------------------------

## Step 1 -- Clone Repository to Docker Host

``` bash
git clone https://github.com/Sonal0409/ecomm.git
```

Now code will be available in:

    /root/ecomm

------------------------------------------------------------------------

## Step 2 -- Run httpd Container with Bind Mount

``` bash
docker run --name myweb -d -P -v /root/ecomm:/usr/local/apache2/htdocs/ httpd
```

------------------------------------------------------------------------

### Command Explanation ⭐

    --name myweb      -> container name
    -d                -> run in background
    -P                -> map exposed ports automatically
    -v                -> bind mount
    /root/ecomm       -> host path
    /usr/local/apache2/htdocs -> apache web root inside container

Whatever files are in `/root/ecomm` → served by Apache.

------------------------------------------------------------------------

## Step 3 -- Verify Container

``` bash
docker ps -a
```

Check which host port is mapped to container port 80.

------------------------------------------------------------------------

## Step 4 -- Access from Browser

Open:

    http://<server-ip>:<mapped-port>

You should see the ecomm application.

------------------------------------------------------------------------

## Step 5 -- Developer Makes Changes

Suppose developer updates code in Git.

We must pull latest code on server.

``` bash
cd ecomm
git pull origin master
```

------------------------------------------------------------------------

## What happens now?

Because container is using bind mount:

🔥 Updated files are already inside the container.

No restart required (unless caching).

Refresh browser → changes visible.

------------------------------------------------------------------------

## Key Difference from Named Volume ⭐

Bind Mount → host controls path\
Named Volume → Docker controls path

Bind mount is tightly coupled with server filesystem.

------------------------------------------------------------------------

## Important Points for Interviews 🔥

-   Mostly used in development.
-   Good for live reload.
-   Risk: container can modify host files.
-   Path must exist on host.
-   If host folder deleted → container data gone.

------------------------------------------------------------------------

## Security Warning ⚠️

Container gets direct access to host filesystem. Wrong permissions can
affect system files.

------------------------------------------------------------------------

## One-Line Memory Trick 🧠

    Bind Mount = Direct host folder inside container

------------------------------------------------------------------------



# Docker Commit -- Create Image from Container

## Concept Overview

When we build images using a Dockerfile, Docker internally follows this
process:

1.  Create a temporary container
2.  Execute a Dockerfile instruction
3.  Commit the container as a new image layer
4.  Repeat for each instruction
5.  Final image = combination of all layers

### Important Points ⭐

-   Each step in Dockerfile = one image layer
-   Temporary containers are automatically removed
-   Final image is immutable and layered
-   This is how Docker builds images behind the scenes

------------------------------------------------------------------------

## ⚠️ Note

Using `docker commit` is **NOT recommended for production**.

✔ Good for learning\
❌ Not reproducible\
❌ No version control\
❌ Hard to maintain

👉 Always prefer **Dockerfile** in real projects.

------------------------------------------------------------------------

# Practical Demo -- Create Custom Image Using docker commit

------------------------------------------------------------------------

## Step 1 -- Run Base Container

``` bash
docker run -it --name cont1 ubuntu
```

------------------------------------------------------------------------

## Step 2 -- Install Required Packages

``` bash
apt-get update && apt-get install -y git curl
```

------------------------------------------------------------------------

## Step 3 -- Exit the Container

``` bash
exit
```

------------------------------------------------------------------------

## Step 4 -- Commit Container to Image

``` bash
docker commit cont1 myubuntu
```

------------------------------------------------------------------------

## Step 5 -- Verify Image

``` bash
docker images
```

------------------------------------------------------------------------

## Step 6 -- Cleanup (Optional)

``` bash
docker rm -f $(docker ps -aq)
```

------------------------------------------------------------------------

## Step 7 -- Run Container from Custom Image

``` bash
docker run -it --name mycont myubuntu
```

------------------------------------------------------------------------

## Step 8 -- Validate

``` bash
git --version
curl --version
```

------------------------------------------------------------------------

## Step 9 -- Exit

``` bash
exit
```

------------------------------------------------------------------------

# Push Image to Docker Hub

------------------------------------------------------------------------

## Login

``` bash
docker login -u <your-username>
```

------------------------------------------------------------------------

## Tag

``` bash
docker tag myubuntu <your-username>/myubuntu
```

------------------------------------------------------------------------

## Push

``` bash
docker push <your-username>/myubuntu
```

------------------------------------------------------------------------

# Summary

    docker commit = snapshot of container
    Dockerfile     = best practice


# Dockerfile

------------------------------------------------------------------------

## What is a Dockerfile?

A **Dockerfile** is a simple text file that contains instructions to
build a Docker image.

### Key Points ⭐

-   No file extension required
-   Name should be `Dockerfile` (case-sensitive best practice)
-   Contains a sequence of instructions
-   Each instruction creates a **layer**
-   Used to automate image creation

------------------------------------------------------------------------

## Syntax

    INSTRUCTION argument

Example:

    FROM ubuntu
    RUN apt-get update

------------------------------------------------------------------------

# Common Dockerfile Instructions

------------------------------------------------------------------------

## 1. FROM (Mandatory)

Defines the base image.

``` dockerfile
FROM ubuntu:22.04
```

✔ Must be the first instruction\
✔ Determines OS/environment

------------------------------------------------------------------------

## 2. RUN

Executes commands during build time.

``` dockerfile
RUN apt-get update && apt-get install -y git
```

### Uses:

-   Install packages
-   Create files/directories
-   Set permissions

✔ Can be used multiple times\
✔ Creates new image layer

------------------------------------------------------------------------

## 3. ENV

Sets environment variables.

``` dockerfile
ENV APP_ENV=production
```

### Access:

    ${APP_ENV}

✔ Available during build + container runtime

------------------------------------------------------------------------

## 4. LABEL

Adds metadata to image.

``` dockerfile
LABEL version="1.0"
LABEL maintainer="Raviteja"
```

✔ Used for documentation\
✔ Helps in filtering images

------------------------------------------------------------------------

## 5. COPY

Copies files from host → container.

``` dockerfile
COPY app/ /usr/src/app/
```

✔ Only local files\
❌ Does NOT extract `.tar` automatically

------------------------------------------------------------------------

## 6. ADD

Similar to COPY but with extra features.

``` dockerfile
ADD app.tar.gz /app/
ADD https://example.com/file.txt /tmp/
```

✔ Extracts `.tar` automatically\
✔ Can download from URL

⚠️ Best Practice: - Prefer `COPY` - Use `ADD` only when needed

------------------------------------------------------------------------

## 7. EXPOSE

Defines container port.

``` dockerfile
EXPOSE 80
```

✔ Informational only\
❌ Does NOT publish port automatically

------------------------------------------------------------------------

## 8. CMD

Default command when container starts.

``` dockerfile
CMD ["nginx", "-g", "daemon off;"]
```

✔ Can be overridden at runtime\
✔ Only last CMD is used

------------------------------------------------------------------------

## 9. ENTRYPOINT

Main command that always runs.

``` dockerfile
ENTRYPOINT ["nginx"]
```

✔ Cannot be overridden easily\
✔ Runtime args are appended

------------------------------------------------------------------------

## CMD vs ENTRYPOINT ⭐

  Feature    CMD               ENTRYPOINT
  ---------- ----------------- ------------------------
  Override   ✅ Yes            ❌ No (only args pass)
  Purpose    Default command   Fixed main command

------------------------------------------------------------------------

## 10. WORKDIR

Sets working directory.

``` dockerfile
WORKDIR /app
```

✔ Like `cd` in Linux

------------------------------------------------------------------------

## 11. USER

Specifies user to run container.

``` dockerfile
USER appuser
```

✔ Improves security

------------------------------------------------------------------------

## 12. VOLUME

Creates mount point.

``` dockerfile
VOLUME /data
```

✔ Used for persistent storage

------------------------------------------------------------------------

# Sample Dockerfile 🔥

``` dockerfile
FROM ubuntu:22.04

RUN apt-get update && apt-get install -y nginx

COPY index.html /var/www/html/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

------------------------------------------------------------------------

# Build Image

``` bash
docker build -t myimage .
```

------------------------------------------------------------------------

# Run Container

``` bash
docker run -d -p 8080:80 myimage
```

------------------------------------------------------------------------

# Important Best Practices ⭐

-   Use minimal base images (alpine)
-   Combine RUN commands to reduce layers
-   Use COPY instead of ADD
-   Avoid root user
-   Use .dockerignore file

------------------------------------------------------------------------

# One-Line Memory Trick 🧠

    Dockerfile = Blueprint to create Docker Image

------------------------------------------------------------------------


# Build an Image from Dockerfile (Nginx HTML Deployment)

------------------------------------------------------------------------

## Goal

-   Create a Dockerfile to run **Nginx**
-   Serve a custom **index.html**
-   Build image and run container
-   Access page in browser

------------------------------------------------------------------------

## Project Structure

    mydockerfiles/
     ├── Dockerfile
     └── index.html

------------------------------------------------------------------------

## Step 1 -- Create Project Directory

``` bash
mkdir mydockerfiles
cd mydockerfiles
```

------------------------------------------------------------------------

## Step 2 -- Create Dockerfile

> ⚠️ Best practice: file name should be `Dockerfile` (capital D)

``` bash
vim Dockerfile
```

### Dockerfile Content

``` dockerfile
# Base image
FROM ubuntu:22.04

# Avoid interactive prompts during build
ENV DEBIAN_FRONTEND=noninteractive

# Install nginx
RUN apt-get update && apt-get install -y nginx && rm -rf /var/lib/apt/lists/*

# Metadata
LABEL description="This is nginx deployment"

# Copy HTML file to nginx web root (Ubuntu nginx default)
COPY index.html /var/www/html/index.html

# Expose container port
EXPOSE 80

# Start nginx in foreground
CMD ["nginx", "-g", "daemon off;"]
```

------------------------------------------------------------------------

## Step 3 -- Create index.html

``` bash
vim index.html
```

``` html
<h1>This file is from Docker</h1>
```

------------------------------------------------------------------------

## Step 4 -- Build Image

``` bash
docker build -t myimagenginx .
```

### Explanation ⭐

-   `-t myimagenginx` → image name
-   `.` → current directory as build context (must contain Dockerfile)

------------------------------------------------------------------------

## Step 5 -- Verify Image

``` bash
docker images
```

------------------------------------------------------------------------

## Step 6 -- Run Container

``` bash
docker run -d -P --name mynginx myimagenginx
```

### Explanation ⭐

-   `-d` → run in background
-   `-P` → maps container port (80) to a random host port
-   `--name` → container name

> Alternative (recommended for predictable port):

``` bash
docker run -d -p 8080:80 --name mynginx myimagenginx
```

------------------------------------------------------------------------

## Step 7 -- Verify Container

``` bash
docker ps -a
```

------------------------------------------------------------------------

## Step 8 -- Access in Browser

If using `-P`, find mapped port from `docker ps`:

    http://<server-ip>:<mapped-port>

If using `-p 8080:80`:

    http://<server-ip>:8080

------------------------------------------------------------------------

# Corrections & Important Notes ⭐

-   Use **Dockerfile** (capital D), not `dockerfile`
-   Combine RUN commands to reduce layers
-   Clean apt cache to reduce image size
-   `EXPOSE` is informational (does not publish port)
-   Prefer `-p` over `-P` for fixed port mapping

------------------------------------------------------------------------

# Best Practice Alternative 🔥

Instead of Ubuntu, use official nginx image:

``` dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

✔ Smaller image\
✔ Faster build\
✔ Production ready

------------------------------------------------------------------------

# One-Line Memory Trick 🧠

    Dockerfile → Build → Image → Run → Container → Browser

------------------------------------------------------------------------


# CMD vs ENTRYPOINT Difference (Docker) – Demo

## Overview

Both **CMD** and **ENTRYPOINT** define commands that run when a container starts, but they behave differently.
```
| Feature             | CMD                                    | ENTRYPOINT                                    |
| ------------------- | -------------------------------------- | --------------------------------------------- |
| Purpose             | Provides **default arguments/command** | Defines the **main command that always runs** |
| Override at runtime | ✔ Yes                                  | ❌ No (arguments are appended instead)         |
| Multiple allowed    | Only last CMD is used                  | Only last ENTRYPOINT is used                  |
| Typical usage       | Default command                        | Main container process                        |
```
---

# Demo – Understanding CMD and ENTRYPOINT

We will create:

1. A **shell script**
2. A **Dockerfile**
3. Build an image
4. Run containers with and without arguments

---

## Step 1 – Create a Shell Script

Create a file:

```bash
vim script.sh
```

Add the following content:

```bash
#!/bin/sh

echo "Running commands using container"
echo "Passing arguments using CMD keyword"

echo "Arguments passed to container: $@"
```

### Explanation
```
| Line        | Meaning                                 |
| ----------- | --------------------------------------- |
| `#!/bin/sh` | Shell interpreter                       |
| `echo`      | Print messages                          |
| `$@`        | Displays arguments passed to the script |

Make script executable later in Dockerfile.
```
---

## Step 2 – Create Dockerfile

Create a Dockerfile:

```bash
vim Dockerfile
```

Add the following:

```dockerfile
FROM ubuntu

WORKDIR /myfiles

COPY script.sh /myfiles/script.sh

RUN chmod +x /myfiles/script.sh

ENTRYPOINT ["sh", "/myfiles/script.sh"]

CMD ["Image", "container"]
```

---

## Dockerfile Instruction Explanation

### FROM

```dockerfile
FROM ubuntu
```

Base image for container.

---

### WORKDIR

```dockerfile
WORKDIR /myfiles
```

Sets working directory inside container.

Equivalent to:

```bash
cd /myfiles
```

---

### COPY

```dockerfile
COPY script.sh /myfiles/script.sh
```

Copies script from host → container.

---

### RUN

```dockerfile
RUN chmod +x /myfiles/script.sh
```

Gives execute permission to the script.

---

### ENTRYPOINT

```dockerfile
ENTRYPOINT ["sh", "/myfiles/script.sh"]
```

Defines the **main command that always runs** when the container starts.

---

### CMD

```dockerfile
CMD ["Image", "container"]
```

Provides **default arguments** to the ENTRYPOINT script.

---

## Step 3 – Build Docker Image

Run:

```bash
docker build -t myimage01 .
```

Explanation:

| Option         | Meaning                           |
| -------------- | --------------------------------- |
| `docker build` | Builds image                      |
| `-t`           | Image name                        |
| `.`            | Current directory (build context) |

---

## Step 4 – Run Container (Default Arguments)

```bash
docker run myimage01
```

### Output

```
Running commands using container
Passing arguments using CMD keyword
Arguments passed to container: Image container
```

### Why?

Because CMD provides default arguments.

---

## Step 5 – Override CMD Arguments

Run container with arguments:

```bash
docker run myimage01 docker kubernetes
```

### Output

```
Running commands using container
Passing arguments using CMD keyword
Arguments passed to container: docker kubernetes
```

### What happened?

Runtime arguments **override CMD**.

But ENTRYPOINT **still runs the script**.

---

## Important Concept

Actual command executed:

```
ENTRYPOINT + CMD
```

Example:

```
sh /myfiles/script.sh Image container
```

If runtime args passed:

```
sh /myfiles/script.sh docker kubernetes
```

---

## Key Learning

### CMD

* Default arguments
* Can be overridden

Example:

```dockerfile
CMD ["nginx"]
```

---

### ENTRYPOINT

* Main command
* Cannot be overridden easily

Example:

```dockerfile
ENTRYPOINT ["nginx"]
```

---

## Best Practice

Use **ENTRYPOINT for main application**
Use **CMD for default arguments**

Example:

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Runtime:

```
docker run myimage script.py
```

---

## Summary

```
ENTRYPOINT → Main command
CMD        → Default arguments
```

Execution format:

```
ENTRYPOINT + CMD
```

Example:

```
sh script.sh Image container
```

---


## Build a python Image

https://github.com/Sonal0409/Jenkins-Python-Pipeline-demo.git

## Build a nodejs image

https://github.com/Sonal0409/nodejsappDockerfile.git





# Multi-Stage Build in Dockerfile

## Overview

Often when building applications, developers need tools like:

-   Build tools
-   Compilers
-   Package managers
-   Testing frameworks

These tools are required **only during the build process**, not when the
application runs.

If we install all these tools inside the final Docker image, the image
becomes:

-   Large in size
-   Contains unnecessary tools
-   Less secure

To solve this problem, Docker provides **Multi-Stage Builds**.

------------------------------------------------------------------------

# Problem Without Multi-Stage Build

Example Dockerfile:

``` dockerfile
FROM ubuntu

RUN apt-get update
RUN apt-get install -y openjdk-11-jdk
RUN apt-get install -y maven

COPY src /

RUN mvn compile
RUN mvn test
RUN mvn package

RUN apt-get install -y tomcat9

COPY target/*.war /var/lib/tomcat9/webapps/

CMD ["catalina.sh", "run"]
```

## What happens here?

The final image contains:

-   Java
-   Maven
-   Build dependencies
-   Test tools
-   Application artifact
-   Tomcat server

### Problem

All build tools remain in the image.

This causes:

-   Large image size
-   Security risks
-   Unnecessary dependencies in production

------------------------------------------------------------------------

# Solution: Multi-Stage Build

A **multi-stage Dockerfile** allows multiple `FROM` statements.

Each `FROM` represents a **new build stage**.

The output from one stage can be used in another stage.

### Key Idea

Only the **final stage** becomes the production image.

------------------------------------------------------------------------

# Advantages of Multi-Stage Build

-   Smaller Docker images
-   No build tools in production image
-   Better security
-   Faster deployments
-   Cleaner Dockerfiles

------------------------------------------------------------------------

# Important Concepts
```
  Concept       Meaning
  ------------- -------------------------------------
  FROM          Starts a new stage
  AS            Gives a name to a stage
  COPY --from   Copies artifacts from another stage
```
------------------------------------------------------------------------

# Multi-Stage Build Demo (Go Application)

Repository used in demo:

https://github.com/Sonal0409/multistagebuild-GoLang.git

Clone repository:

``` bash
git clone https://github.com/Sonal0409/multistagebuild-GoLang.git
cd multistagebuild-GoLang
```

------------------------------------------------------------------------

# Explanation of Dockerfile

## Stage 1 -- Build Stage

``` dockerfile
FROM ubuntu AS build
```

FROM ubuntu → Pull Ubuntu image from Docker Hub\
AS build → Name this stage **build**

Whenever Docker sees `FROM`, a **new stage begins**.

------------------------------------------------------------------------

### Install Go Compiler

``` dockerfile
RUN apt-get update && apt-get install -y golang-go
```

Installs Go compiler and build tools required to build the application.

------------------------------------------------------------------------

### Set Environment Variable

``` dockerfile
ENV GO111MODULE=off
```

Disables Go modules.

------------------------------------------------------------------------

### Copy Source Code

``` dockerfile
COPY . .
```

Copies application source code from host to container.

------------------------------------------------------------------------

### Build Application

``` dockerfile
RUN go build -o /app .
```

Compiles the Go application and creates a binary file named **/app**.

------------------------------------------------------------------------

# Stage 2 -- Final Production Image

``` dockerfile
FROM scratch
```

`scratch` is an empty base image containing no OS or libraries.

------------------------------------------------------------------------

### Copy Binary From Build Stage

``` dockerfile
COPY --from=build /app /app
```

Copies the compiled binary `/app` from the **build stage** to the final
container.

------------------------------------------------------------------------

### Run the Application

``` dockerfile
ENTRYPOINT ["/app"]
```

Runs the compiled application when the container starts.

------------------------------------------------------------------------

# Build the Image

``` bash
docker build -t go-multistage .
```

------------------------------------------------------------------------

# Run the Container

``` bash
docker run -p 8080:8080 go-multistage
```

------------------------------------------------------------------------

# Build Flow Visualization

Stage 1 (build stage)

Ubuntu\
Install Go\
Compile application\
Create binary /app

Stage 2 (final stage)

Scratch image\
Copy /app from build stage\
Run application

------------------------------------------------------------------------

# Result

Final image contains only:

scratch\
/app

No build tools, no dependencies.

This makes the image **very small and secure**.

------------------------------------------------------------------------

# Key Interview Points

-   Each `FROM` starts a new stage
-   `AS` names a stage
-   `COPY --from` copies artifacts between stages
-   Only the **last stage becomes the final image**
-   Significantly reduces image size

------------------------------------------------------------------------

# One-Line Memory Trick

Multi-Stage Build = Build tools in one stage, deploy only the compiled
artifact in the final stage.



# Docker Voting Application -- Architecture & Dockerfiles

Repository:
https://github.com/Sonal0409/Docker-Stack-VotingApp/tree/main/example-voting-app

This project demonstrates a **microservices architecture using Docker
containers**.

The application is composed of multiple services that communicate with
each other.

------------------------------------------------------------------------

# Application Architecture

## System Flow

The voting application consists of five main services:

1.  Python Web Application
2.  Redis Database
3.  .NET Worker Service
4.  PostgreSQL Database
5.  Node.js Result Application

## Flow Explanation

1.  User accesses the Python voting web application
2.  User submits a vote
3.  Vote is temporarily stored in Redis
4.  .NET Worker service reads votes from Redis
5.  Worker processes votes and stores them in PostgreSQL
6.  Node.js result application reads results from PostgreSQL
7.  Results are displayed to users

------------------------------------------------------------------------

# Architecture Overview
```
  User
  ↓ 
  Python Vote App
  ↓ 
  Redis 
  ↓ 
  .NET Worker 
  ↓ 
  PostgreSQL 
  ↓ 
  Node.js Result App
```
------------------------------------------------------------------------
# Python Voting Application Dockerfile (Multi-Stage)

This Dockerfile is used to build the **Python voting application** in the Docker Voting App project.

It uses a **multi-stage build** to:

* Install dependencies in one stage
* Use them in the final stage
* Keep the final image cleaner and reproducible

---

# Stage 1 — Base Stage

```dockerfile
# Use official lightweight Python image
FROM python:3.11-slim AS base

# Set working directory inside the container
# All commands will run relative to this directory
WORKDIR /usr/local/app

# Copy dependency file from host → container
# requirements.txt contains all Python libraries needed by the application
COPY requirements.txt ./requirements.txt

# Install all Python dependencies listed in requirements.txt
# pip installs packages such as Flask, Redis client, etc.
RUN pip install -r requirements.txt
```

## Explanation

### Base Image

```
python:3.11-slim
```

* Official Python image from Docker Hub
* `slim` version = smaller size
* Contains Python runtime and minimal OS libraries

---

### `AS base`

```
AS base
```

This names the stage **base**.

Later stages can reuse it using:

```
FROM base
```

---

### `WORKDIR`

```
WORKDIR /usr/local/app
```

Creates and switches to the directory:

```
/usr/local/app
```

Equivalent Linux commands:

```
mkdir -p /usr/local/app
cd /usr/local/app
```

All future commands run inside this folder.

---

### Copy dependency file

```
COPY requirements.txt ./requirements.txt
```

Copies:

```
Host machine → Container
requirements.txt → /usr/local/app/requirements.txt
```

Example content of `requirements.txt`:

```
Flask
Redis
gunicorn
```

---

### Install dependencies

```
RUN pip install -r requirements.txt
```

Installs all Python libraries required by the application.

Example:
```
| Package  | Purpose               |
| -------- | --------------------- |
| Flask    | Web framework         |
| Redis    | Redis database client |
| gunicorn | Production web server |
```
---

# Stage 2 — Final Stage

```dockerfile
# Start a new stage based on the previous stage (base)
# This stage already contains Python + installed dependencies
FROM base AS final

# Copy entire application code into container
# Includes app.py and other project files
COPY . .

# Inform Docker that the container listens on port 80
# (Documentation purpose only)
EXPOSE 80

# Command executed when the container starts
# Runs Gunicorn web server and loads the Flask application
CMD ["gunicorn", "app:app", "-b", "0.0.0.0:80", "--log-file", "-", "--access-logfile", "-", "--workers", "4", "--keep-alive", "0"]
```

---

# Explanation

## `FROM base AS final`

Instead of rebuilding everything again, this stage **reuses the base stage**.

So the container already contains:

* Python runtime
* Installed dependencies
* required libraries

This saves build time.

---

## Copy application code

```
COPY . .
```

Copies all project files from host to container:

Example:

```
app.py
requirements.txt
templates/
static/
```

Now the container has the full Python application.

---

# `EXPOSE 80`

```
EXPOSE 80
```

Indicates that the application runs on port **80**.

Important:

* This does **not publish the port**
* It is only documentation for Docker.

Port is published using:

```
docker run -p 8080:80 image
```

---

# `CMD` — Start the Application

```
CMD ["gunicorn", "app:app", "-b", "0.0.0.0:80", "--log-file", "-", "--access-logfile", "-", "--workers", "4", "--keep-alive", "0"]
```

This starts the **Gunicorn web server**.

---

## Gunicorn Breakdown

### Run gunicorn

```
gunicorn
```

Gunicorn is a **production-grade Python web server**.

---

### Application location

```
app:app
```

Format:

```
file_name:object_name
```

Meaning:

```
app.py → file
app → Flask application object
```

Example Python code:

```python
app = Flask(__name__)
```

---

### Bind server to port

```
-b 0.0.0.0:80
```

Means:

```
Listen on all network interfaces
Use port 80
```

So the application becomes accessible from outside the container.

---

### Logging

```
--log-file -
```

Send logs to **stdout**.

```
--access-logfile -
```

Send request logs to **stdout**.

Why?

Because Docker best practice is:

```
Application logs → STDOUT
```

Then logs can be viewed using:

```
docker logs <container>
```

---

### Workers

```
--workers 4
```

Starts **4 worker processes**.

This allows multiple users to access the application simultaneously.

Example:

```
User1 → Worker1
User2 → Worker2
User3 → Worker3
User4 → Worker4
```

---

### Keep Alive

```
--keep-alive 0
```

Closes connection immediately after request.

Good for high-throughput containerized workloads.

---

# Example Run Command

Build image:

```
docker build -t voting-app .
```

Run container:

```
docker run -p 8080:80 voting-app
```

Access application:

```
http://localhost:8080
```

---

# Flow Inside Container

```
Container Starts
        ↓
CMD executes
        ↓
Gunicorn starts
        ↓
Loads Flask app (app.py)
        ↓
Application listens on port 80
        ↓
Users can access the voting app
```

---

# Key DevOps Concepts Demonstrated

* Multi-stage Docker build
* Dependency caching
* Production web server (Gunicorn)
* Container logging best practices
* Scalable Python web service

---

# One-Line Summary

```
Stage 1 → Install Python dependencies
Stage 2 → Copy app and run Gunicorn server
```

---



------------------------------------------------------------------------

# .NET Worker Dockerfile (Multi‑Stage)

Stage 1 -- Build Stage

``` dockerfile
FROM mcr.microsoft.com/dotnet/sdk:7.0 as build

WORKDIR /App

COPY . ./

RUN dotnet publish -c release -o out
```

Explanation:

-   Uses full .NET SDK to compile code
-   Produces compiled artifact **Worker.dll**

------------------------------------------------------------------------

Stage 2 -- Runtime Stage

``` dockerfile
FROM mcr.microsoft.com/dotnet/runtime:7.0

WORKDIR /app

COPY --from=build /App/out .

ENTRYPOINT ["dotnet", "Worker.dll"]
```

Explanation:

Only runtime dependencies are included.

Benefits:

-   Smaller image
-   No build tools
-   Faster deployment

------------------------------------------------------------------------
# Node.js Result Application Dockerfile

``` dockerfile
FROM node:18-slim

RUN apt-get update && \
    apt-get install -y --no-install-recommends curl tini && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /usr/local/app

RUN npm install -g nodemon

COPY package*.json ./

RUN npm ci && \
    npm cache clean --force && \
    mv /usr/local/app/node_modules /node_modules

COPY . .

ENV PORT 80
EXPOSE 80

ENTRYPOINT ["/usr/bin/tini", "--"]
CMD ["node", "server.js"]
```

## Explanation

### Base Image

Uses Node.js slim image to keep container lightweight.

### Install Tools

-   curl → health checks
-   tini → proper container init process

### Working Directory

Application code runs from `/usr/local/app`.

### Dependency Installation

`npm ci` installs dependencies based on package-lock.json.

### ENTRYPOINT + CMD

Actual execution becomes:

tini -- node server.js


------------------------------------------------------------------------

# Key DevOps Concepts Demonstrated

This project demonstrates:

-   Microservices architecture
-   Multi-stage Docker builds
-   Different language runtimes
-   Container communication
-   Production container best practices

------------------------------------------------------------------------

# Summary
```
  Service      Technology     Purpose
  ------------ -------------- ------------------------
  Vote         Python         Accept user votes
  Redis        In-memory DB   Temporary vote storage
  Worker       .NET           Process votes
  PostgreSQL   Database       Store final results
  Result       Node.js        Display voting results
```
------------------------------------------------------------------------

# Key Learning

Multiple containers can work together to form a **complete application
system using Docker**.


# Docker Dangling Images

## What Are Dangling Images?

In Docker, **dangling images** are images that have:

```
<none>:<none>
```

as their **repository name and tag**.

Example when you run:

```bash
docker images
```

Output may look like:

```
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
myapp        latest    8a1f2b3c4d5e   2 minutes ago  150MB
<none>       <none>    6f7a8b9c0d1e   5 minutes ago  150MB
```

The image with `<none>:<none>` is called a **dangling image**.

---

# Why Dangling Images Are Created

Dangling images are created when:

* A new image build **replaces an old image layer**
* Multi-stage builds create **intermediate images**
* Image builds are **interrupted or updated**

Docker keeps these images temporarily because they may still be referenced during builds.

---

# Dangling Images in Multi-Stage Builds

In a **multi-stage Docker build**, each stage produces an intermediate image.

Example:

```dockerfile
FROM node AS build
RUN npm install

FROM nginx
COPY --from=build /app /usr/share/nginx/html
```

Here:

* `build` stage creates an intermediate image
* Only the **last stage** becomes the **final image**

The intermediate images may appear as:

```
<none>:<none>
```

These are dangling images.

---

# How to List Dangling Images

To list only dangling images:

```bash
docker images -f dangling=true
```

Example output:

```
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
<none>       <none>    6f7a8b9c0d1e   10 minutes ago 150MB
```

---

# Removing Dangling Images

## Remove dangling images (interactive)

```bash
docker image prune
```

Docker will ask:

```
Are you sure you want to continue? [y/N]
```

---

## Force remove dangling images

```bash
docker image prune -f
```

The `-f` option skips the confirmation prompt.

---

## Remove All Unused Images

```bash
docker image prune -a
```

This removes:

* dangling images
* unused images (not referenced by any container)

⚠️ Be careful with this command because it may remove images you still need.

---

# Example Workflow

### Step 1 — Build an image

```bash
docker build -t myapp .
```

### Step 2 — Build again after changes

```bash
docker build -t myapp .
```

The previous image may become:

```
<none>:<none>
```

### Step 3 — Remove dangling images

```bash
docker image prune
```

---

# Quick Cleanup Command

Many DevOps engineers use:

```bash
docker system prune
```

This removes:

* stopped containers
* unused networks
* dangling images

---

# Summary
```
| Concept         | Description                            |
| --------------- | -------------------------------------- |
| Dangling Images | Images with `<none>:<none>`            |
| Cause           | Intermediate layers or replaced builds |
| Safe to Remove  | Yes, if not used                       |
| Cleanup Command | `docker image prune`                   |
```
---

# One-Line Memory Trick

```
Dangling Images = Unused intermediate images left after builds
```

---


# CI/CD – Jenkins Docker Pipeline

This pipeline demonstrates how to build and deploy a **Java web application using Jenkins and Docker**.

The application produces a **WAR file** which is deployed on **Tomcat**.

---

# How the Application Works

Tomcat automatically deploys any **`.war` file** placed in:

```
$CATALINA_HOME/webapps
```

When the WAR file is placed there:

1. Tomcat **extracts the WAR**
2. Deploys the application
3. Makes it available through a browser

Example URL:

```
http://<publicIP>:<port>/addressbook
```

Example:

```
http://192.168.1.10:8080/addressbook
```

---

# Jenkins Permission for Docker

Jenkins must have permission to run Docker commands.

Run this command on the server:

```bash id="b8f60q"
# Give permission to Jenkins user to access Docker socket
# Docker CLI communicates with Docker daemon through this socket
chmod -R 777 /var/run/docker.sock
```

⚠️ **Note**

This is not recommended for production due to security risks.

Better approach:

```bash id="55fl1c"
sudo usermod -aG docker jenkins
```

---

# Jenkins Pipeline Overview

This pipeline performs the following steps:

```
1. Clone source code from GitHub
2. Build Java application using Maven
3. Build Docker image
4. Push image to DockerHub
5. Deploy container using Docker
```

---

# Jenkins Pipeline Code

```groovy id="gr7e5x"
pipeline{

    # Jenkins can run the pipeline on any available agent/node
    agent any

    # Define tools required for the pipeline
    tools{
        # Use Maven tool configured in Jenkins global tools
        maven 'mymaven'
    }

    stages{

        stage('clone the repo'){
            steps{
                # Clone source code from GitHub repository
                git 'https://github.com/Sonal0409/DevOpsCodeDemo.git'
            }
        }

        stage('Build Code'){
            steps{
                # Build Java project using Maven
                # clean -> removes previous build artifacts
                # package -> creates WAR file
                sh 'mvn clean package'
            }
        }

        stage('Build Image'){
            steps{
                # Build Docker image from Dockerfile in repository
                # -t assigns a tag/name to the image
                sh 'docker build -t myjavajenkins .'
            }
        }

        stage('login and push Image'){
            steps{

                # Use Jenkins credentials stored securely
                # DOCKERHUB_TOKEN is stored in Jenkins credentials manager
                withCredentials([string(credentialsId: 'DOCKERHUB_TOKEN', variable: 'DOCKERHUB_PASSWORD')]) {

                    # Login to DockerHub
                    # Password is taken securely from Jenkins credential store
                    sh 'docker login -u sonal04 -p ${DOCKERHUB_PASSWORD}'
                }

                # Tag image with DockerHub repository name
                sh 'docker tag myjavajenkins sonal04/myjavajenkins'

                # Push image to DockerHub registry
                sh 'docker push sonal04/myjavajenkins'
            }
        }

        stage('Deploy the Image'){
            steps{

                # Run the container in detached mode
                # -d runs container in background
                # -P maps container ports automatically to host ports
                sh 'docker run -d -P sonal04/myjavajenkins'
            }
        }

    }
}
```

---

# Pipeline Stage Explanation

## Stage 1 — Clone Repository

```
git clone
```

Downloads application source code from GitHub.

Repository:

```
https://github.com/Sonal0409/DevOpsCodeDemo.git
```

---

## Stage 2 — Build Code

```
mvn clean package
```

Actions:
```
| Command | Purpose                      |
| ------- | ---------------------------- |
| clean   | Removes previous build files |
| package | Creates `.war` file          |
```
Example output:

```
target/addressbook.war
```

---

## Stage 3 — Build Docker Image

```
docker build -t myjavajenkins .
```

Creates Docker image using the **Dockerfile** in the repository.

Image name:

```
myjavajenkins
```

---

## Stage 4 — Push Image to DockerHub

### Login

```
docker login
```

Uses Jenkins credentials to authenticate.

### Tag Image

```
docker tag myjavajenkins sonal04/myjavajenkins
```

Format:

```
<dockerhub-username>/<image-name>
```

### Push Image

```
docker push sonal04/myjavajenkins
```

Uploads image to DockerHub registry.

---

## Stage 5 — Deploy Container

```
docker run -d -P sonal04/myjavajenkins
```

Options:
```
| Option | Meaning                     |
| ------ | --------------------------- |
| -d     | Run container in background |
| -P     | Automatically map ports     |
```
Example port mapping:

```
0.0.0.0:32768 -> 8080
```

Application URL becomes:

```
http://<server-ip>:32768/addressbook
```

---

# Complete CI/CD Flow

```
Developer pushes code
        ↓
Jenkins triggers pipeline
        ↓
Clone GitHub repo
        ↓
Build WAR using Maven
        ↓
Build Docker image
        ↓
Push image to DockerHub
        ↓
Run container on server
        ↓
Application deployed
```

---

# Key DevOps Concepts Used

* Jenkins Pipeline
* Maven build automation
* Docker image creation
* DockerHub registry
* Credential management
* Automated deployment

---

# One-Line Summary

```
GitHub → Jenkins → Maven Build → Docker Image → DockerHub → Container Deployment
```

---


# CI/CD – GitHub Actions Pipeline (Maven + Docker)

Repository Example:

```id="b8xy8y"
https://github.com/Sonal0409/MavenBuild-Docker-GitHubActions.git
```

This GitHub Actions workflow automatically:

1. Clones the code
2. Builds a Java application using Maven
3. Builds a Docker image
4. Pushes the image to DockerHub

This is a **CI/CD pipeline using GitHub Actions**.

---

# Workflow File Location

GitHub Actions workflow files must be placed in:

```id="je1x9a"
.github/workflows/
```

Example file:

```id="7uvq4b"
.github/workflows/build.yml
```

---

>> For actions marketplace URL : https://github.com/marketplace?query=docker&type=actions


# GitHub Actions Workflow (With Comments)

```yaml id="u3brws"
# Name of the workflow visible in GitHub Actions UI
name: build java Application

# Define the event that triggers the workflow
on:
  push   # run pipeline whenever code is pushed to the repository

jobs:

  # Define the job name
  builddeployjob:

    # GitHub will create a virtual machine with Ubuntu
    runs-on: ubuntu-latest

    steps:

      # Step 1: Checkout repository code
      - name: Checkout code
        uses: actions/checkout@v4
        # This action clones the GitHub repository into the runner

      # Step 2: Install Java and Maven
      - name: Install Java and Maven
        uses: actions/setup-java@v4
        with:
          java-version: '17'      # Install Java 17
          distribution: 'temurin' # OpenJDK distribution
          cache: maven            # Cache Maven dependencies to speed up builds

      # Step 3: Setup Docker build environment
      - name: Install docker
        uses: docker/setup-buildx-action@v3
        # Enables Docker BuildKit and advanced Docker builds

      # Step 4: Build Java application using Maven
      - name: build the code
        run: mvn package
        # mvn package compiles the project and generates a WAR/JAR file

      # Step 5: Login to DockerHub
      - name: Loginto Docker Hub
        uses: docker/login-action@v3
        with:
          username: sonal04                     # DockerHub username
          password: ${{ secrets.DOCKERHUB_TOKEN }}
          # Password is stored securely in GitHub Secrets

      # Step 6: Build Docker image and push to DockerHub
      - name: Build dockerfile and push Image
        uses: docker/build-push-action@v6
        with:
          context: .                # directory containing Dockerfile
          tags: sonal04/myimagejava # image name
          push: true                # push image to DockerHub
```

---

# Explanation of Key Sections

## Workflow Name

```id="t9f3er"
name: build java Application
```

This name appears in the **GitHub Actions dashboard**.

---

# Trigger

```id="ec0d0a"
on:
  push
```

The workflow runs whenever code is pushed to the repository.

Example:

```id="rf8c82"
git push origin main
```

---

# Job Definition

```id="rc7lb8"
jobs:
  builddeployjob
```

A **job** represents a group of steps executed on a runner.

---

# Runner Environment

```id="sg3jsk"
runs-on: ubuntu-latest
```

GitHub creates a **temporary Ubuntu virtual machine** to run the pipeline.

---

# Step 1 — Checkout Code

```id="68eyz2"
uses: actions/checkout@v4
```

Clones the repository into the runner environment.

Equivalent to:

```id="k9x3o4"
git clone <repo-url>
```

---

# Step 2 — Install Java & Maven

```id="xk3j1n"
uses: actions/setup-java@v4
```

Installs:

```id="6qtg9a"
Java 17
Maven
```

Also enables dependency caching.

Benefits:

* faster builds
* avoids downloading dependencies repeatedly

---

# Step 3 — Setup Docker

```id="amq9mp"
docker/setup-buildx-action
```

Enables **Docker Buildx**, which allows:

* advanced builds
* multi-platform builds
* faster image builds

---

# Step 4 — Build Java Application

```id="cbn4f4"
mvn package
```

This command:

1. Compiles Java source code
2. Runs tests
3. Packages application

Output example:

```id="l1mxq4"
target/myapp.war
```

---

# Step 5 — Login to DockerHub

```id="t3kbb5"
docker/login-action
```

Logs into DockerHub using credentials stored in:

```id="md3yzt"
GitHub Secrets
```

Secret used:

```id="q0d7c1"
DOCKERHUB_TOKEN
```

Secrets are configured in:

```id="vhyh9r"
Repository Settings → Secrets → Actions
```

---

# Step 6 — Build and Push Docker Image

```id="ty9d3k"
docker/build-push-action
```

This step:

1. Reads the **Dockerfile**
2. Builds the Docker image
3. Pushes it to DockerHub

Example image:

```id="igxklc"
sonal04/myimagejava
```

---

# Example Pipeline Flow

```id="q9u2k8"
Developer pushes code
        ↓
GitHub Actions triggered
        ↓
Checkout repository
        ↓
Install Java & Maven
        ↓
Build application
        ↓
Build Docker image
        ↓
Push image to DockerHub
```

---

# Example Final Image

After the workflow runs successfully, the image is available at:

```id="klbqts"
https://hub.docker.com/r/sonal04/myimagejava
```

You can run the container using:

```id="3d2xui"
docker run -p 8080:8080 sonal04/myimagejava
```

---

# Key DevOps Concepts Demonstrated

* GitHub Actions CI/CD
* Maven build automation
* Docker image creation
* DockerHub registry integration
* Secure secret management

---

# One-Line Summary

```id="7r3c5m"
GitHub Push → GitHub Actions → Maven Build → Docker Image → DockerHub
```

---

# Docker Network – Class Summary

Docker networking allows containers to communicate with:

* other containers
* the host machine
* the internet

Docker provides multiple network types. The most important one is the **Bridge Network (docker0)**.

---

# Docker Network Types

Main network types:
```
| Network Type     | Purpose                                 |
| ---------------- | --------------------------------------- |
| Bridge (default) | Default container communication network |
| Custom Bridge    | User-defined network with DNS support   |
| Host             | Container uses host network directly    |
| None             | Container has no network                |
``
---

# Default Bridge Network (docker0)

When Docker is installed, it automatically creates a network called:

```
docker0
```

This network uses the **bridge driver**.

### Key Characteristics

* Automatically created
* Containers communicate using **IP addresses**
* No **DNS name resolution**
* IP addresses may change when containers restart

---

# Docker Default Network Flow

```
Container
    │
    │ (veth cable)
    ▼
docker0 bridge
    │
    ▼
Host Network
    │
    ▼
Internet
```

---

# How Containers Connect to docker0

When a container is created:

1. Docker creates a **virtual ethernet pair (veth cable)**
2. One end connects to container
3. Other end connects to **docker0 bridge**

Example communication path:

```
Container
   ↓
veth cable
   ↓
docker0 bridge
   ↓
Host machine
   ↓
Internet
```

---

# Key Concepts

### 1. Host Network

Your EC2 server has its own network.

Example:

```
AWS Network
     ↓
EC2 Host Network
```

---

### 2. Docker Network

When Docker is installed:

```
Host Network
      ↓
Docker Bridge Network (docker0)
      ↓
Containers
```

All containers by default connect to this **bridge network**.

---

# Container Communication in Default Network

Containers inside the same network can communicate using:

```
IP address
```

Example:

```
Container A (172.17.0.2)
      │
      ▼
Container B (172.17.0.3)
```

However, they **cannot communicate using container names**.

Example that fails:

```
ping cont2
```

Example that works:

```
ping 172.17.0.3
```

---

# Problem with Default Bridge Network

Problem:

* container IP changes when recreated
* services break if IP changes

Example:

```
Container A → connect to 172.17.0.3
Container B recreated → IP becomes 172.17.0.5
Communication breaks
```

This becomes difficult when managing **hundreds of microservices**.

---

# Solution – Custom Bridge Network

Instead of using the default bridge network, we create a **custom network**.

Advantages:

* DNS name resolution
* stable service communication
* better isolation

Example communication:

```
Container A
     │
     ▼
ping cont2
     │
     ▼
Container B
```

---

# Creating Custom Bridge Network

```
docker network create --driver=bridge edureka1
```

Now containers in this network can communicate via:

* container name
* IP address

---

# Container Isolation

Containers in **different networks cannot communicate**.

Example:

```
Network1
   └── Container A

Network2
   └── Container B
```

A cannot reach B.

---

# Multi-Network Container

A container can connect to **multiple networks**.

Example:

```
Network1        Network2
   │                │
   ▼                ▼
Container C (connected to both)
```

This container can communicate with containers in both networks.

---

# Host Network

In host networking mode:

```
docker run --network host
```

Container uses the **host machine network directly**.

Example:

```
Container
   │
   ▼
Host Network
```

### Characteristics

* No port mapping required
* container shares host IP
* commonly used in **Kubernetes components**

Example:

```
docker run -d --network=host nginx
```

Check ports:

```
ss -tulwn
```

You will see port **80 already in use**.

---

# None Network

This network provides **complete isolation**.

```
docker run --network none
```

Container will have:

* no IP address
* no internet
* no communication

Example:

```
Container
   │
   ▼
No Network
```

---

# Important Networking Components

## Virtual Ethernet Cable (veth)

When container is created:

```
Container
   │
   │ veth cable
   ▼
docker0 bridge
```

This allows communication with the host.

Check veth cables:

```
ip link show | grep veth
```

---

# NAT (Network Address Translation)

Docker uses NAT so containers can access the internet.

Flow:

```
Container
   ↓
docker0 bridge
   ↓
NAT (iptables)
   ↓
Host network
   ↓
Internet
```

Check NAT rules:

```
iptables -t nat -L -n -v
```

---

# Demo Commands

## Check Host Network

```
ip a
```

---

## List Docker Networks

```
docker network ls
```

---

## Check NAT Configuration

```
iptables -t nat -L -n -v
```

---

## Check Virtual Ethernet Cables

```
ip link show | grep veth
```

---

# Demo – Custom Bridge Network

Create network:

```
docker network create --driver=bridge edureka1
```

---

Create first container:

```
docker run -itd --name=cont1 --network=edureka1 busybox
```

Inspect container:

```
docker inspect cont1
```

---

Create second container:

```
docker run -itd --name=cont2 --network=edureka1 busybox
```

Inspect container:

```
docker inspect cont2
```

Now containers can communicate via:

```
ping cont2
```

---

# Demo – Host Network

```
docker run -d --network=host --name=n1 nginx
```

Check port usage:

```
ss -tulwn
```

---

# Demo – None Network

```
docker run -itd --name n2 --network none busybox
```

Container will not have an IP address.

---

# Multi-Network Demo

### Step 1 – Create networks

```
docker network create mynet
docker network create mynet2
```

---

### Step 2 – Container in mynet

```
docker run -itd --name=c1 --network=mynet busybox
```

---

### Step 3 – Container in mynet2

```
docker run -itd --name=c2 --network=mynet2 busybox
```

Observation:

```
c1 cannot communicate with c2
```

---

### Step 4 – Container in both networks

```
docker run -itd --name=c3 --network=mynet2 --network=mynet busybox
```

Observation:

```
c3 can communicate with c1 and c2
```

---

# Final Network Architecture Example

```
            mynet
              │
              ▼
          Container C1

              │
              ▼
        Container C3
              │
              ▼
          Container C2
              ▲
              │
            mynet2
```

---

# Best Practice

When deploying **microservices**, always use:

```
Custom Bridge Networks
```

Avoid using the **default docker0 bridge network**.

---

# One-Line Summary

```
docker0 → default network
custom bridge → production microservices network
host → container shares host network
none → no networking
```

---
