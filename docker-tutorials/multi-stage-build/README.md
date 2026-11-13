# Docker Multi-Stage Build for a React Application

A step-by-step guide to containerizing a React application using a Docker multi-stage build, built with Node.js and served with Nginx on an Ubuntu EC2 instance.

📄 [Full tutorial PDF](./doc/Docker%20Multi-Stage%20Build%20for%20React%20Application.pdf)

---

## Overview

Docker multi-stage builds provide an efficient way to create production-ready container images. Instead of including all development dependencies in the final image, the application is built in one stage and only the required build artifacts are copied into a lightweight production image.

In this tutorial, a React application is built using Node.js and deployed with Nginx. The final image contains only the production-ready files, resulting in a smaller and more secure container.

## Technology Stack

| Technology | Purpose |
|------------|---------|
| Ubuntu Server | Development environment |
| Docker | Containerization platform |
| React.js | Frontend application |
| Node.js | Build environment |
| Nginx | Production web server |

## Objectives

- Create a React application for deployment
- Deploy the application's static files using Nginx
- Optimize the Docker image using a multi-stage build
- Create a lightweight production image by excluding unnecessary build dependencies

## Prerequisites

Docker, Node.js, and npm must be installed on the Ubuntu server before proceeding.

**Install Docker**

```bash
sudo apt install docker.io -y
```

**Verify Docker installation**

```bash
docker --version
```

**Install Node.js**

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
```

**Verify Node.js and npm installation**

```bash
node --version
npm --version
```

---

## Step 1: Create the React Application

Create a working directory.

```bash
mkdir react-docker-demo
cd react-docker-demo
```

Create a React application using Vite.

```bash
npm create vite@latest react-app
```

When prompted, select:

- **Framework:** React
- **Variant:** JavaScript

Navigate to the project directory and install the dependencies.

```bash
cd react-app
npm install
```

## Step 2: Configure the Project for Docker

Create a `.dockerignore` file to exclude unnecessary files from the build context. This reduces the image size and improves build performance.

```bash
touch .dockerignore
nano .dockerignore
```

Add the following entries:

```
node_modules
.git
.gitignore
README.md
dist
```

## Step 3: Create the Multi-Stage Dockerfile

Create and open the Dockerfile.

```bash
touch Dockerfile
nano Dockerfile
```

Paste the following code:

```dockerfile
# Stage 1 - Build Stage
FROM node:22 AS builder

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

# Stage 2 - Production Stage
FROM nginx:alpine

COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### How it works

| Stage | Base Image | What it does |
|-------|------------|--------------|
| Build | `node:22` | Installs dependencies and builds the React app into static files |
| Production | `nginx:alpine` | Serves only the built files from the `dist` folder |

Only the output of the build stage is copied into the final image, so Node.js and the development dependencies never reach production.

## Step 4: Build and Run the Docker Image

Build the Docker image.

```bash
docker build -t react-docker-app .
```

Verify that the image has been created.

```bash
docker images
```

Run the container.

```bash
docker run -d -p 8080:80 --name react-app react-docker-app
```

Verify the container is running.

```bash
docker ps
```

## Step 5: Verify the Deployment

Open a web browser and navigate to:

```
http://<EC2-Public-IP>:8080
```

The React application should load, confirming that it is being served by Nginx from the container.

> Make sure port `8080` is allowed in the EC2 instance's security group.

## Step 6: Cleanup

Stop the running container.

```bash
docker stop react-app
```

Remove the container.

```bash
docker rm react-app
```

Remove the Docker image.

```bash
docker rmi react-docker-app
```

---

## Conclusion

In this tutorial, a simple React application was created and containerized using a Docker multi-stage build. The application was built using Node.js in the first stage and deployed using Nginx in the second stage. By excluding development dependencies from the final image, the resulting container is lightweight, efficient, and suitable for production deployments.
