
---

## **1. Python (Flask/Django App)**

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

✅ Key Points: Use `requirements.txt`, keep dependencies cached, expose 5000.

---

## **2. Node.js (Express App)**

```dockerfile
FROM node:16-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

✅ Key Points: `COPY package*.json` before code to optimize caching.

---

## **3. Java (Spring Boot JAR)**

```dockerfile
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/myapp.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

✅ Key Points: Package first with Maven/Gradle, then copy only the JAR.

---

## **4. Go (Statically Compiled App)**

*(Uses multi-stage build for small image)*

```dockerfile
# Build stage
FROM golang:1.19 AS builder
WORKDIR /src
COPY . .
RUN go build -o app

# Final stage (small image)
FROM alpine:3.17
WORKDIR /app
COPY --from=builder /src/app .
EXPOSE 8080
CMD ["./app"]
```

✅ Key Points: Multi-stage reduces image size from 800MB → <20MB.

---

## **5. Nginx (Static Website Hosting)**

```dockerfile
FROM nginx:alpine
COPY ./html /usr/share/nginx/html
EXPOSE 80
```

✅ Key Points: Just copy static files into Nginx’s default directory.

---

## **6. Multi-Stage Example (React App + Nginx)**

```dockerfile
# Stage 1: Build React app
FROM node:16-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Serve with Nginx
FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
EXPOSE 80
```

✅ Key Points: Node builds React → Nginx serves it. Production ready!

---

## **7. Common Best Practices**

* Always use **small base images** (`alpine` when possible).
* Use **multi-stage builds** for compiled languages.
* Add a **.dockerignore** file to skip unnecessary files (`.git`, `node_modules`, `__pycache__`).
* Always **pin versions** (e.g., `python:3.9-slim` instead of `python:latest`).
* Run containers as **non-root user** for security.
* Use **HEALTHCHECK** for production containers.

---

✅ With this cheatsheet you can:

* Answer *“Write a Dockerfile for X app”* instantly.
* Show you know **optimizations** (multi-stage, alpine, caching).
* Stand out with **security tips** (non-root, secrets).

---
