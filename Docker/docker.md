---

### **Section 1 – Basics (1–10)**

1. **What is Docker?**

   * Docker is a platform to build, package, and run applications inside isolated containers, ensuring consistency across environments.

2. **Difference between VM and Container?**

   * VM runs a full OS on a hypervisor; container shares the host OS kernel, making it lightweight and faster.

3. **What is a Docker Image?**

   * A read-only template containing application code, dependencies, and settings.

4. **What is a Docker Container?**

   * A running instance of an image.

5. **Difference between `docker run` and `docker start`?**

   * `docker run` creates a new container; `docker start` restarts an existing stopped container.

6. **What is a Docker Volume?**

   * Persistent storage for containers, managed by Docker.

7. **What is Docker Compose?**

   * Tool to define and run multi-container apps via YAML.

8. **Docker Registry vs Repository?**

   * Registry is a service that hosts repositories; repository is a collection of images.

9. **Difference between ENTRYPOINT and CMD?**

   * `ENTRYPOINT` defines the main command; `CMD` provides default arguments or a fallback command.

10. **What is `.dockerignore` used for?**

    * To exclude files from being sent to the Docker daemon during build.

---

### **Section 2 – Intermediate (11–20)**

11. **How do you reduce Docker image size?**

    * Use `alpine` base, multi-stage builds, clean up apt cache, `.dockerignore`.

12. **How does Docker networking work?**

    * Uses a virtual bridge (`docker0`); containers communicate via internal IPs.

13. **Difference between bridge and host network mode?**

    * Bridge gives container private IP; host shares host’s network stack.

14. **What are Docker storage drivers?**

    * Mechanisms like `overlay2`, `aufs` for managing layered filesystems.

15. **How do you debug a failing container?**

    * `docker logs`, `docker exec`, `docker inspect` for exit codes.

16. **How to pass environment variables?**

    * `-e VAR=value` or `env_file` in Docker Compose.

17. **Bind mounts vs Volumes?**

    * Bind mounts map host path; volumes are managed by Docker.

18. **How do you handle secrets?**

    * Docker secrets in Swarm or external tools like Vault.

19. **What is a Multi-stage build?**

    * Technique to reduce final image size by separating build and runtime stages.

20. **How to expose ports in Docker?**

    * `EXPOSE` in Dockerfile (metadata) or `-p host:container` when running.

---

### **Section 3 – Advanced (21–35)**

21. **How do you secure Docker?**

    * Use rootless mode, scan images, sign images, limit capabilities.

22. **What is Docker Content Trust (DCT)?**

    * Enables digital signing of images.

23. **Difference between `docker exec` and `docker attach`?**

    * `exec` starts a new process; `attach` connects to existing process STDOUT/STDIN.

24. **What is an orphan container?**

    * Container not part of the current Compose project but still running.

25. **How to limit CPU and Memory for a container?**

    * `--cpus`, `--memory` flags in `docker run`.

26. **Explain `docker system prune`.**

    * Cleans unused containers, networks, and dangling images.

27. **How do you check container resource usage?**

    * `docker stats`.

28. **What is `docker inspect` used for?**

    * Displays detailed container/image/network metadata.

29. **How do you copy files between host and container?**

    * `docker cp hostfile container:/path` and vice versa.

30. **What is an ephemeral container?**

    * A short-lived container, often used for debugging.

31. **How do you troubleshoot slow builds?**

    * Optimize Dockerfile layer order, cache dependencies first.

32. **What is a dangling image?**

    * Unnamed images (`<none>`) from intermediate builds.

33. **How to change default Docker network driver?**

    * Use `docker network create --driver <driver>`.

34. **How do you persist DB data in Docker?**

    * Use named volumes.

35. **How to handle image pull rate limits?**

    * Authenticate to Docker Hub or use private registry.

---

### **Section 4 – Scenario Root Cause & Fix (36–45)**

36. **Container restarts repeatedly**

    * **Cause:** Wrong entrypoint or dependency missing.
    * **Fix:** Check logs, correct entrypoint, add healthcheck.

37. **Image too large**

    * **Cause:** Large base image, unoptimized layers.
    * **Fix:** Use `alpine`, multi-stage builds.

38. **Port inaccessible**

    * **Cause:** Port not mapped.
    * **Fix:** Use `-p host:container`.

39. **Data lost after restart**

    * **Cause:** No volume mount.
    * **Fix:** `-v name:/path`.

40. **Build slow**

    * **Cause:** No caching optimization.
    * **Fix:** Reorder Dockerfile.

41. **Cannot pull image**

    * **Cause:** Auth or network issue.
    * **Fix:** `docker login`, check proxy.

42. **Permission denied inside container**

    * **Cause:** Wrong user.
    * **Fix:** Use `USER` in Dockerfile or `--user` flag.

43. **Container time not matching host**

    * **Cause:** Timezone difference.
    * **Fix:** Mount `/etc/localtime`.

44. **High CPU usage**

    * **Cause:** App bug or infinite loop.
    * **Fix:** Limit CPU, debug app.

45. **Docker daemon not starting**

    * **Cause:** Corrupt metadata or disk full.
    * **Fix:** Clean `/var/lib/docker` (carefully).

---

### **Section 5 – Hands-on & Commands (46–50)**

46. **Write a Dockerfile for a Node.js app**

```dockerfile
FROM node:16-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

47. **Run MySQL with volume & env vars**

```bash
docker run -d --name mydb \
  -e MYSQL_ROOT_PASSWORD=root \
  -v mysql_data:/var/lib/mysql \
  -p 3306:3306 mysql:8
```

48. **Useful Docker commands**

```bash
docker build -t myapp .
docker run -d --name myapp -p 8080:80 myapp
docker ps -a
docker logs -f myapp
docker exec -it myapp /bin/bash
docker system prune -f
```

49. **Docker Compose example**

```yaml
version: "3.8"
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: root
    volumes:
      - db_data:/var/lib/mysql
volumes:
  db_data:
```

50. **Inspect container metadata**

```bash
docker inspect myapp
```

---
