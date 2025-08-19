
## **Sample Dockerfile (All-In-One Example)**

```dockerfile
# 1. Base image
FROM python:3.9-slim AS builder

# 2. Metadata about image
LABEL maintainer="hema@example.com"
LABEL project="flask-app"

# 3. Build-time variable (ARG)
ARG APP_HOME=/app

# 4. Environment variable (ENV)
ENV PYTHONUNBUFFERED=1 \
    APP_ENV=production

# 5. Working directory
WORKDIR $APP_HOME

# 6. Copy application code
COPY requirements.txt ./

# 7. Run commands inside container (install deps)
RUN pip install --no-cache-dir -r requirements.txt

# 8. Copy rest of the code
COPY . .

# 9. Add remote/local files (similar to COPY but with extraction ability)
ADD https://example.com/config.tar.gz /tmp/

# 10. Expose port
EXPOSE 5000

# 11. Define volumes for persistent storage
VOLUME ["/app/logs"]

# 12. Switch to non-root user
RUN useradd -m appuser
USER appuser

# 13. Healthcheck to monitor container
HEALTHCHECK --interval=30s --timeout=5s --retries=3 CMD curl -f http://localhost:5000/health || exit 1

# 14. Command to run (default)
CMD ["python", "app.py"]

# 15. Alternative main entrypoint
# ENTRYPOINT ["python", "app.py"]

# 16. Onbuild trigger (for child images)
ONBUILD RUN echo "This runs when another image is built FROM this image"
```

---

## **Explanation of Dockerfile Instructions**

1. **FROM** → Defines base image.

   * Example: `FROM ubuntu:20.04`
   * Can use multi-stage builds (`AS builder`).

2. **LABEL** → Adds metadata.

   * Example: `LABEL version="1.0"`

3. **ARG** → Build-time variable (available only during `docker build`).

   * Example: `ARG APP_HOME=/app`

4. **ENV** → Runtime environment variable (persists inside container).

   * Example: `ENV APP_ENV=production`

5. **WORKDIR** → Sets working directory inside container.

   * Example: `WORKDIR /app`

6. **COPY** → Copies files from host to container.

   * Example: `COPY . /app`

7. **RUN** → Executes command inside container (build-time).

   * Example: `RUN apt-get update && apt-get install -y curl`

8. **ADD** → Like `COPY` but can also extract tar files or download URLs.

   * Example: `ADD https://example.com/file.tar.gz /tmp/`

9. **EXPOSE** → Documents ports app listens on (not mandatory).

   * Example: `EXPOSE 8080`

10. **VOLUME** → Declares mount points for persistent storage.

    * Example: `VOLUME ["/data"]`

11. **USER** → Switches container user.

    * Example: `USER appuser`

12. **HEALTHCHECK** → Defines a command to check container health.

    * Example: `HEALTHCHECK CMD curl -f http://localhost/ || exit 1`

13. **CMD** → Default command when container starts (can be overridden).

    * Example: `CMD ["python", "app.py"]`

14. **ENTRYPOINT** → Defines main executable that always runs.

    * Example: `ENTRYPOINT ["python", "app.py"]`
    * Difference: `CMD` can be overridden, `ENTRYPOINT` cannot (unless `--entrypoint` used).

15. **ONBUILD** → Trigger instruction that runs when image is used as a base for another image.

    * Example: `ONBUILD COPY . /app`

---

✅ **Interview Tip:**
They may ask:

* *“Difference between RUN vs CMD vs ENTRYPOINT?”*

  * **RUN** → executes at build time (creates image layers).
  * **CMD** → runs at container start (default command, overridable).
  * **ENTRYPOINT** → main container process (not easily overridden).

---
