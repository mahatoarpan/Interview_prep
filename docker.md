# Docker

### What is Docker, and how is it different from a virtual machine?
Docker is an open-source platform that allows you to build, ship, and run applications in containers. 
* A container is a lightweight, standalone, and executable package that contains everything needed to run an application (code, runtime, libraries, dependencies, configs).
* Docker ensures applications run the same way across different environments (dev, test, prod).

| **Aspect**         | **Docker (Containers)**                                           | **Virtual Machines (VMs)**                                 |
| ------------------ | ----------------------------------------------------------------- | ---------------------------------------------------------- |
| **Abstraction**    | Containers virtualize at the **OS level** (share host OS kernel). | VMs virtualize at the **hardware level** (via hypervisor). |
| **Resource usage** | Lightweight, smaller footprint (MBs).                             | Heavyweight, requires full OS per VM (GBs).                |
| **Startup time**   | Starts in **seconds**.                                            | Takes **minutes** (boot full OS).                          |
| **Isolation**      | Process-level isolation using Linux namespaces & cgroups.         | Strong isolation since each VM runs its own OS.            |
| **Portability**    | Highly portable — same image runs anywhere Docker is installed.   | Less portable — tied to specific hypervisor/OS.            |
| **Performance**    | Near-native performance (low overhead).                           | Higher overhead due to extra OS layers.                    |


### What are containers, and why are they useful?
A container is a lightweight, portable unit that packages an application and its dependencies so it can run consistently across environments. They’re useful because they’re fast, resource-efficient, portable, and solve the ‘works on my machine’ problem.

### What is the difference between Docker image and Docker container?
A Docker image is a read-only blueprint that contains application code and dependencies. A container is a running instance of that image, with its own writable layer and runtime environment.

Example:
1. You pull an Nginx image (`nginx:latest`)
2. When you run it (`docker run nginx`), it creates a container running nginx
3. You can run multiple containers from the same image.


### What is the difference between docker run and docker start?
`docker run` creates a new container from an image and starts it, while `docker start` just restarts an already existing stopped container. If we perform `docker run` multiple times with the same image it creates a new container each time.

### Explain the Docker architecture (Client, Daemon, Registry).
Docker uses a client-server architecture. The Docker Client is the CLI tool that users interact with. The Docker Daemon (dockerd) runs on the host and manages images, containers, networks, and volumes. The Docker Registry (like Docker Hub) stores images, which the daemon pulls from or pushes to as needed.

1. Docker client: The CLI or API tool that user interacts with. When user run commands like `docker run`, `docker build`, or `docker ps`, the client sends these requests to the Docker Daemon uing REST APIs.

2. Docker Daemon (dockerd): The engine that does all the heacy lifting. It is responsible for building images, running containers, managing networks and volumes.

3. Docker Registry: A repository for images (public or private). 

Workflow:
1. You run → docker run nginx (Client).
2. Client sends request to Daemon.
3. Daemon checks if nginx image exists locally: If not, it pulls from Registry (e.g., Docker Hub).
4. Daemon creates and starts a container from that image.


### How do you persist data in Docker containers?
There are two main ways to persist data in Docker containers:
1. Volumes (Preferred way):
    * Managed by Docker.
    * Stored outside the container's lifecycle. Data persists even if the container is deleted.
    * Best for portability, sharing data between containers, and production use.
    * Volumes persist even if the container is deleted (unless explicitly removed with docker volume rm).

```bash
docker run -d -v myvolume:/data mysql
# Here myvolume is a Docker-managed volume.
```
2. Bind Mounts:
    * Maps a host machine directory to a container directory.
    * Best when you want to access/edit files directly from the host.

```bash
docker run -d -v /host/data:/container/data nginx
# /host/data is a directory on the host.
# /container/data is where the container reads/writes data.
```

### What is the difference between COPY and ADD in a Dockerfile?
COPY only copies files from the build context into the image, while ADD can also extract tar archives and fetch files from URLs. Best practice is to use COPY for clarity and only use ADD if you need its extra features. ADD’s extra features can cause unintended side effects, making builds less predictable.

```bash
COPY ./app /usr/src/app # just copies files into container.
ADD app.tar.gz /usr/src/app/ # Automatically extract app.tar.gz inside the container
```

### How do you reduce the size of a Docker image?
1. Choose a smaller base image: Use alpine or slim versions (e.g., `python:3.11-alpine` instead of `python:3.11`).
```dockerfile
FROM python:3.11-alpine 
```

2. Use multi-stage builds: Build tools/libraries in one stage, copy only the needed artifacts into the final stage.
```dockerfile
FROM golang:1.20 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp

FROM alpine:latest
COPY --from=builder /app/myapp /usr/local/bin/myapp
CMD ["myapp"]
```

3. Remove unnecessary packages/files: Clean up cache, temp files, package managers.

4. Use `.dockerignore`: Exclude unnecessary files (e.g., logs, node_modules, .git)

5. Combine RUN commands: Reduce intermediate layers by chaining commands.
```dockerfile
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*
```

6. Install only production dependencies

7. Use specific COPY instead of copying everything: Avoid `COPY . .`.


### How do you share environment variables with a Docker container?

1. Using `-e` flag in docker run command
```bash
docker run -d -e DB_HOST=localhost -e DB_USER=admin myapp
```

2. Using `--env-file` option:
    * Keep env variables in a file (.env)
    * Run container with the file
```bash
docker run --env-file .env myapp
```

3. Define in dockerfile: But this makes them hardcoded and visible in the image. Not ideal for secrets.
```dockerfile
ENV DB_HOST=localhost
ENV DB_USER=admin
```

4. Docker Compose
```yml
version: '3'
services:
  app:
    image: myapp
    environment:
      - DB_HOST=localhost
      - DB_USER=admin
```

### What is the difference between docker-compose and Docker Swarm?
| **Aspect**            | **Docker Compose**                                                             | **Docker Swarm**                                                                          |
| --------------------- | ------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| **Purpose**           | Tool to define and run **multi-container applications** on a **single host**.  | Orchestration tool for running containers across a **cluster of machines**.               |
| **Scope**             | Local development, small-scale setups.                                         | Production-level orchestration, scaling, and clustering.                                  |
| **How it works**      | Uses a `docker-compose.yml` file to define services, networks, volumes.        | Uses `docker swarm init` and `docker service` to deploy containers across multiple nodes. |
| **Scaling**           | Can scale containers (`docker-compose up --scale web=3`) but only on one host. | Automatically balances load and scales across multiple nodes.                             |
| **High Availability** | ❌ Not built-in.                                                                | ✅ Provides built-in HA and failover.                                                      |
| **Networking**        | Simple bridge networking on a single host.                                     | Overlay networking across multiple nodes.                                                 |
| **Use Case**          | Great for **development & testing**.                                           | Better for **production deployments** when Kubernetes isn’t used.                         |


### How can you limit CPU and memory usage for a Docker container?
Use `--memory` or `-m` flag in docker run.
```bash
docker run -d -m 512m nginx
# Limits container memory to 512MB.
# If the container tries to use more, it may be likked (OOMKilled).
```

Limit CPU shares (relative weight)
```bash
docker run -d --cpu-shares=512 nginx
# Default is 1024. Lower = less priority when competing for CPU.
```

Limit specific CPU cores
```bash
docker run -d --cpuset-cpus="0,1" nginx
# Container will only run on CPU core 0 and 1.
```

Limit CPU quota
```bash
docker run -d --cpus="1.5" nginx
# Restricts container to 1.5 CPU cores worth of time.
```

Docker compose:
```yaml
version: "3.8"
services:
  app:
    image: myapp
    deploy:
      resources:
        limits:
          cpus: "1.0"
          memory: 512M

```

### What happens when you run docker run -d -p 8080:80 nginx?
1. docker run
    * Creates and starts a new container from the nginx image.

2. Image check (nginx)
    * Docker checks if the nginx image exists locally.
    * If not, it pulls it from Docker Hub (default registry).

3. -d (detached mode)
    * Runs the container in the background.
    * Without -d, it would run in the foreground and attach logs to your terminal.

4. -p 8080:80 (port mapping)
    * Maps host port 8080 → to container port 80.
    * Container runs Nginx on port 80, but you access it on http://localhost:8080.

5. Run Nginx
    * The container starts the default ENTRYPOINT/CMD of the Nginx image, which runs the Nginx server.

### How do you update a running container?

You don’t directly update a running container since containers are immutable. Instead, you update the image, stop and remove the old container, and then start a new container from the updated image. For resource limits, you can use docker update, but for application changes you always redeploy a new container.

1. Update the image:
    * Make changes (e.g., update code, install a new package).
    * Rebuild the image:
```bash
docker build -t myapp:v2 .
```

2. Stop and remove the old container
```bash
docker stop myapp_container
docker rm myapp_container
```

3. Run a new container with the updated image
```bash
docker run -d --name myapp_container -p 8080:80 myapp:v2
```

### How does Docker networking work (bridge, host, overlay)?

When you start a container, it gets attached to a network driver. The most common ones are:

1. Bridge Network (default)
    * Default driver if you don’t specify anything.
    * Creates a private internal network on the host.
    * Containers on the same bridge can talk to each other using container names.
    * Ports must be published with -p to be accessed from outside.

```bash
docker run -d --name web1 nginx
docker run -d --name web2 nginx
# web1 can reach web2 on the bridge via http://web2:80.
# From the host, you need -p 8080:80 to access it.
```

2. Host Network
    * Container shares the host’s network stack (no isolation).
    * No port mapping needed — container uses host’s IP directly.
    * Useful for high-performance apps that need low latency (but less isolation).

```bash
docker run -d --network host nginx
# Nginx runs directly on host’s port 80.
```

3. Overlay Network
    * Used in multi-host / swarm mode.
    * Creates a distributed network that spans multiple Docker hosts.
    * Containers on different hosts can communicate securely as if on the same LAN.

```bash
docker network create -d overlay my_overlay
# Then services/containers in a swarm can attach to my_overlay and talk to each other across nodes.
```

### What is the role of docker-compose.override.yml?

When you run docker-compose up, Docker Compose automatically looks for two files:
1. `docker-compose.yml` → The base configuration (services, networks, volumes, etc.).
2. `docker-compose.override.yml` → The override configuration.

If present, the docker-compose.override.yml merges with and overrides the base docker-compose.yml. It’s mainly used to keep the base file clean for production while customizing things like volumes, ports, or environment variables for development. Docker Compose automatically applies it when you run docker-compose up.

### How do multi-stage builds work in Docker? Why are they useful?

In a Dockerfile, you can define multiple `FROM` instructions. Each `FORM` starts a new stage. You can selectively copy artifacts from one stage to another using `COPY --from=<stage>`. Only the final stage becomes your image.

```Dockerfile
# Stage 1: Build -- Uses full Go SDK to compile
FROM golang:1.21 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp

# Stage 2: Runtime -- Only includes the compiled binary + dependencies. Final image is tiny (Alpine + binary), not bloated with compilers.
FROM alpine:3.19
WORKDIR /app
COPY --from=builder /app/myapp .
CMD ["./myapp"]

```

Benefits:
1. Smaller Image Size
    * Don’t need to ship compilers, build tools, or intermediate files.
    * Only keep the essentials.

2. Better Security
    * Fewer tools in the runtime image = smaller attack surface.

3. Cleaner Separation of Concerns
    * Build stage: code compilation, tests, packaging.
    * Runtime stage: just run the app.

4. Flexibility
    * You can have multiple stages (test stage, debug stage, final runtime).
    * Can reuse intermediate artifacts without bloating.

### How does Docker handle isolation (cgroups, namespaces)? // TODO

### How does Docker ensure image layer reusability and caching?
A Docker image is made of layers, created by each instruction in the Dockerfile (FROM, COPY, RUN, etc). Each line is a new layer. Layers are readonly and stacked using a union filesystem. Final container gets a writable layer on top.

```Dockerfile
FROM ubuntu:20.04     # Layer 1
RUN apt-get update    # Layer 2
RUN apt-get install -y python3 # Layer 3
COPY . /app           # Layer 4
CMD ["python3", "app.py"] # Layer 5

```

When building, Docker checks the instruction already been executed before with the same context? If yes, reuse the cached layer instead of rebuilding. If no, rebuild from this step onward (and discard cache for later steps). Example: If you change a single file, only the steps after COPY will rebuild. Earlier steps remain cached.

### What are some best practices for writing Dockerfiles?
1. Choose the right base image:
    * Use small, secure images like alpine instead of heavy ones like Ubuntu unless required.
    * Use official images whenever possible.

2. Leverage multi-stage builds:
    * Keep build tools in one stage and copy only necessary artifacts into the final image.
    * Reduces image size dramatically.

3. Optimize layer caching:
    * Place less frequently changing commands first. Example: Install dependencies before copying app code.

```Dockerfile
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
```

4. Combine and clean up RUN commands
    * Each RUN adds a layer → merge commands with `&&`. Clean package cache after install.
```Dockerfile
RUN apt-get update && apt-get install -y \
    curl \
    git \
 && rm -rf /var/lib/apt/lists/*

```

5. Use .dockerignore
    * Exclude unnecessary files (.git, node_modules, logs, docs).
    * Reduces build context and speeds up builds.

6. Use COPY instead of ADD (unless you need ADD features)
    * COPY is explicit for copying files.
    * Use ADD only when extracting tar files or pulling from URLs.

7. Run as non-root user
    * Security best practice → avoid running as root inside container.

```dockerfile
RUN adduser -D appuser
USER appuser
```

8. Pin versions for reproducibility, Avoid “latest” tags.
```dockerfile
FROM node:20.5-alpine
```

9. Set ENTRYPOINT and CMD properly
    * ENTRYPOINT → defines the fixed executable.
    * CMD → provides default arguments (overridable).

```dockerfile
ENTRYPOINT ["python", "app.py"]
CMD ["--port", "8080"]
```

### What is the difference between Docker Swarm and Kubernetes?
Docker Swarm is simpler, lightweight, and easier to set up — good for smaller clusters or quick deployments. Kubernetes is far more powerful and scalable, offering auto-scaling, self-healing, advanced networking, and persistent storage — making it the industry standard for production at scale.

| Feature                    | **Docker Swarm**                                                                       | **Kubernetes**                                                                                  |
| -------------------------- | -------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| **Setup & Learning Curve** | Very easy to set up (`docker swarm init`), simple CLI, minimal YAML.                   | Complex setup, steep learning curve, requires kube-api, etcd, controllers.                      |
| **Complexity & Features**  | Lightweight, fewer features, simpler orchestration.                                    | Full-featured orchestration: auto-scaling, self-healing, custom controllers, operators.         |
| **Scalability**            | Suitable for smaller clusters (tens to a few hundred nodes).                           | Designed for massive scale (thousands of nodes).                                                |
| **Networking**             | Uses Docker’s overlay network, simple built-in load balancer.                          | Advanced networking with CNI plugins (Calico, Flannel, Cilium), supports network policies.      |
| **Load Balancing**         | Internal routing mesh, automatic load balancing across nodes.                          | Services with ClusterIP, NodePort, LoadBalancer, Ingress controllers for advanced traffic mgmt. |
| **High Availability**      | Replicated services, but limited failover options.                                     | Strong HA — pods rescheduled automatically, control plane HA supported.                         |
| **Storage**                | Basic volume support, limited drivers.                                                 | Rich persistent volume system, dynamic provisioning, storage classes.                           |
| **Ecosystem & Community**  | Small ecosystem, less adoption, not actively developed (Docker is focusing elsewhere). | Huge ecosystem, CNCF project, industry standard, large community.                               |
| **Use Cases**              | Quick deployments, small-to-medium teams, dev/test environments.                       | Large-scale production workloads, multi-cloud/hybrid infra, enterprise-grade orchestration.     |


### How would you deploy a multi-container application (e.g., frontend + backend + DB)?
To deploy a multi-container app like frontend, backend, and DB, I’d usually use docker-compose in development. Each service is defined in a docker-compose.yml file with networking, volumes, and environment variables, so they can talk using service names. For production, I’d move to Kubernetes or Docker Swarm for orchestration, scaling, and resilience.

```yaml
version: "3.9"
services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
  backend:
    build: ./backend
    ports:
      - "5000:5000"
    environment:
      - DB_HOST=db
    depends_on:
      - db
  db:
    image: postgres:15
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:

```

### A containerized app needs to talk to a database running on the host — how would you configure it?

If a container needs to connect to a DB on the host, the approach depends on the environment. On Mac/Windows, I’d use `host.docker.internal`. On Linux, I’d use the host’s bridge interface IP (commonly 172.17.0.1), or run the container with `--network host` so it can access localhost. In production, I’d usually avoid this setup and run the DB in its own container or external managed DB service, then connect via proper networking.

Using `--network host` in production is discouraged because it bypasses Docker’s network isolation. Containers share the host’s network stack, which can cause port conflicts, security risks, and loss of portability. It’s fine for debugging or performance testing, but in production, it’s better to use user-defined bridge or overlay networks for proper isolation.

