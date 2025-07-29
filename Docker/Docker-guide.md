# 🐳 Docker Interview Prep – Beginner to Pro

---

## ✅ Section 1: Docker Basics

### ❓ 1. What is Docker?
Docker is an open-source containerization platform that packages applications and dependencies into containers that can run uniformly across environments.

---

### ❓ 2. What is the difference between Docker Images and Containers?
- **Image**: A read-only template used to create containers.
- **Container**: A running instance of an image.

---

### ❓ 3. How is Docker different from VMs?
- Containers share the host OS kernel → lightweight and fast.
- VMs have separate OS → heavier and slower.
- Docker containers start in milliseconds and consume fewer resources.

---

### ❓ 4. What is a Dockerfile?
A Dockerfile is a script that contains instructions (`FROM`, `RUN`, `COPY`, etc.) to build a Docker image.

---

## ✅ Section 2: Multi-Stage Build

### ❓ 5. What is a multi-stage build in Docker?
A Dockerfile with multiple `FROM` statements to build artifacts in one stage and copy only the final output into a minimal base image. This reduces image size and improves security.

```
# Stage 1 - Builder
FROM golang:1.21 as builder
WORKDIR /app
COPY . .
RUN go build -o myapp

# Stage 2 - Final image
FROM alpine:latest
COPY --from=builder /app/myapp /myapp
ENTRYPOINT ["/myapp"]
```

---

### ❓ 6. Benefits of Multi-Stage Builds
- Smaller and optimized images
- Removes unnecessary build-time dependencies
- Better performance and security
- Clean separation of build and runtime

---

## ✅ Section 3: docker buildx and Multi-Platform Images

### ❓ 7. What is `docker buildx`?
`buildx` is an extended Docker CLI plugin that enables:
- Multi-platform builds (e.g., linux/amd64, linux/arm64)
- Better caching and performance
- Exporting images in OCI format

```
# Create a new builder instance
docker buildx create --name multiarch-builder --use
docker buildx inspect --bootstrap

# Build multi-platform image
docker buildx build --platform linux/amd64,linux/arm64 \
  -t myrepo/myapp:1.0 \
  --push .
```

---

### ❓ 8. Why use `docker buildx` in CI/CD?
- To build images for different CPU architectures
- Ensures cross-platform compatibility for IoT, mobile, and cloud deployments
- Helps push images to remote registries directly

---

## ✅ Section 4: Docker Compose & Networking

### ❓ 9. What is Docker Compose?
Docker Compose is a tool to define and run multi-container applications using a YAML configuration file.

```
version: '3'
services:
  web:
    image: nginx
    ports:
      - "8080:80"
  app:
    build: .
    depends_on:
      - db
  db:
    image: postgres
    volumes:
      - dbdata:/var/lib/postgresql/data
volumes:
  dbdata:
```

---

### ❓ 10. Benefits of Docker Compose in DevOps
- Easy to manage multi-service applications
- Simplifies testing and local development
- Works well in Jenkins or GitLab CI runners

---

## ✅ Section 5: Docker in CI/CD (Jenkins)

### ❓ 11. How is Docker used in CI/CD pipelines?
- Build application images in pipelines
- Push images to DockerHub or ECR
- Deploy using Compose or Kubernetes

---

### ❓ 12. Jenkins + Docker Real-Time Use Case
- Jenkins pulls code → builds Docker image
- Pushes to DockerHub/ECR
- Triggers deployment pipeline

```
pipeline {
  agent any
  stages {
    stage('Build Docker Image') {
      steps {
        script {
          docker.build('myapp:latest')
        }
      }
    }
    stage('Push Image') {
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {
            docker.image('myapp:latest').push('1.0.0')
          }
        }
      }
    }
  }
}

```

---

## ✅ Section 6: Docker Security, Troubleshooting & Best Practices

### ❓ 13. How do you secure Docker containers?
- Use non-root users inside containers
- Use minimal base images
- Scan images with tools like Trivy or Grype
- Set resource limits for CPU and memory
- Avoid using `latest` tag

---

### ❓ 14. How do you manage secrets in Docker?
- Avoid putting secrets in `ENV` or Dockerfile
- Use secret mounting at runtime
- Use secret managers (Vault, AWS Secrets Manager)

---

### ❓ 15. What are common restart policies in Docker?
- `--restart=always`
- `--restart=unless-stopped`
- `--restart=on-failure`

---

### ❓ 16. How do you handle image versioning?
- Use semantic tags like `1.0.0`, `v1.2`, not `latest`
- Automate tagging in CI/CD with Git tags or commit SHA

---

## ✅ Section 7: Real-Time Issues and Fixes

| 🔍 Issue                        | 🛠 Root Cause                             | ✅ Fix                                               |
|-------------------------------|-------------------------------------------|-----------------------------------------------------|
| Container exits immediately   | Bad CMD or ENTRYPOINT                     | Use `tail -f /dev/null` or fix script entrypoint    |
| App not accessible on browser | Port not exposed or mapped                | Use `EXPOSE` in Dockerfile and `-p` in `docker run` |
| Image size too large          | Unoptimized layers                        | Use multi-stage builds, `.dockerignore`             |
| Jenkins fails to run Docker   | Jenkins user not in docker group          | Add Jenkins to `docker` group                       |
| Data lost after container exit| No volumes used                           | Use named volumes or bind mounts                    |

---

## ✅ Section 8: Pro-Level Questions

### ❓ 17. Difference between CMD and ENTRYPOINT?
- `CMD`: Provides defaults that can be overridden.
- `ENTRYPOINT`: Defines the main command that always runs.

Best practice: Use both together for flexibility.

---

### ❓ 18. What is `.dockerignore` used for?
To exclude files and folders (like `.git`, `node_modules`) from the build context to reduce image size and speed up builds.

---

### ❓ 19. What are Docker logging options?
- `json-file` (default)
- `syslog`
- `fluentd`
- `awslogs`

Use these to forward logs to external systems like ELK or Loki.

---

### ❓ 20. How to clean up unused Docker resources?
Use:
- `docker system prune`
- `docker image prune`
- `docker volume prune`

Automate cleanup with cronjobs or scheduled GitOps workflows.

---

## ✅ Section 9: Hands-On Practice Tasks

| Task                                                   | Objective                                        |
|--------------------------------------------------------|--------------------------------------------------|
| Build image using Dockerfile                           | Understand image creation                        |
| Optimize Dockerfile using multi-stage                  | Reduce size and improve performance              |
| Build multi-platform images using `buildx`             | Ensure cross-platform support                    |
| Setup a Compose file for app + DB                      | Work with multi-container stacks                 |
| Write Jenkins pipeline to build and push Docker image  | CI/CD integration with registries                |
| Scan image with Trivy or Grype                         | Improve image security posture                   |
| Debug crashing container with `logs` and `exec`        | Develop troubleshooting experience               |

---

### ❓ 22. Real-Time Issue: Container Fails Instantly and Not Visible in `docker ps -a`

#### 💥 Problem:
- Container fails to start immediately after `docker run`
- It's not visible in `docker ps -a`
- Logs are not available via `docker logs`

---

#### 🧠 Possible Root Causes:
- Invalid or non-existent image/tag
- Broken or missing `CMD`/`ENTRYPOINT`
- Entry script lacks execute permissions
- Minimal base image (e.g., `scratch`) without shell
- Docker daemon or low-level runtime issue

---

#### ✅ Troubleshooting Steps:

1. **Test image shell access**:
   Run container with custom entrypoint:
   > docker run --rm --entrypoint /bin/sh <image>

2. **Check image availability**:
   > docker pull <image_name>

3. **Inspect image metadata**:
   > docker image inspect <image>

4. **Check Docker daemon/system logs**:
   > journalctl -u docker.service --no-pager -e  
   > dmesg | tail -50

5. **Fallback run for debugging**:
   > docker run -it --entrypoint tail <image> -f /dev/null

---

#### 🧯 Fixes:
| Cause                         | Fix                                      |
|------------------------------|-------------------------------------------|
| Invalid tag or image         | Check spelling and pull image manually    |
| Broken ENTRYPOINT script     | Ensure script is valid and `chmod +x`     |
| Missing binary/app           | Confirm app is copied in Dockerfile       |
| No shell in base image       | Use `/bin/sh` instead of `/bin/bash`      |
| CMD fails instantly          | Replace with debug `tail -f /dev/null`    |

---

#### 🧠 Tip:
Add fallback in `Dockerfile` CMD for debugging:

```Dockerfile
CMD ["sh", "-c", "your_app || tail -f /dev/null"]
```
This keeps the container alive for inspection.

## 🏁 Final Tip:
- Be ready to explain how you **used Docker in a real-time project**, including:
  - Containerization of monolithic/microservices apps
  - Dockerizing Jenkins/NGINX/SonarQube/Nexus
  - Using Docker in multi-stage pipelines
  - Docker and Kubernetes integration

---

