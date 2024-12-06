---
title: "Using NGINX as a Load Balancer"
date: 2024-12-06
draft: false
tags: ["devops", "nginx", "docker", "spring boot", "load balancing"]
---

## What is NGINX?

NGINX is a versatile tool that functions as an HTTP web server, reverse proxy, content cache, load balancer, TCP/UDP proxy server, and mail proxy server. In this blog, we’ll focus on using NGINX as a **load balancer** for a static web server application.

### Overview of the Setup

In this tutorial, we’ll walk through setting up a load-balanced environment for a simple static web page server using **Spring Boot**, **Docker**, and **NGINX**. Here's the high-level workflow:

1. **Static Web Server App**:  
   A Spring Boot application will automatically serve static HTML files placed in its `/static` directory.  

2. **Docker**:  
   We’ll use Docker to run multiple instances of the Spring Boot app on different ports via a `docker-compose` file.  

3. **Configuring NGINX**:  
   NGINX will be configured to distribute incoming traffic across the multiple app instances, acting as the load balancer.

---

### 1. Building the Static Web Server App

Let’s create a simple Spring Boot application that serves static HTML files:

- Place your HTML files in the `src/main/resources/static/` directory of your Spring Boot project.
- Run the Spring Boot application. By default, it serves the static files at `http://localhost:8080`.

---

### 2. Dockerizing the Application

To scale the Spring Boot application, we’ll containerize it using Docker. Here’s a basic `Dockerfile` for the app:

```dockerfile
FROM openjdk:17-jdk-slim
COPY target/static-web-app.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]```

Build the docker image : 
```bash
docker build -t static-web-app .
```

Create a `docker-compose.yml` file to run containers of the same app on multiple ports. We will create 3 instances of the container and bind them to ports 3001, 3002 and 3003 respectively.

```yaml
services:
  app1:
    build: .
    environment:
      - APP_NAME=App1
    ports:
      - 3001:8080
  
  app2:
    build: .
    environment:
      - APP_NAME=App2
    ports:
      - 3002:8080

  app3:
    build: .
    environment:
      - APP_NAME=App3
    ports:
      - 3003:8080
```

Run the `docker-compose.yml` file :
```bash
docker-compose up --build
```
`--build` parameter will rebuild the docker image. At this point, the web page should be accessible at `localhost:300x` ports. Now we need to set up `nginx` to distribute traffic across these apps.

### 3. Configuring NGINX

Install `nginx` using your package manager. To configure `nginx`, we need to edit the file `nginx.conf` which you can locate using the command:
```bash
nginx -t
```

Here's a basic NGINX config file to balance traffic between app instances:

```conf
worker_processes 1; # Defines the number of worker processes nginx uses
                    # Set to 1 for low-traffic environments

events {

worker_connections 1024; # This defines the number of simultaneous connections   # each worker process can have


}
 

http {

  

upstream spring_cluster { # Defines load balancing group name.

least_conn; # By default nginx uses round-robin. We use least_conn.


server 127.0.0.1:3001;

server 127.0.0.1:3002;

server 127.0.0.1:3003;

}

  

server { # handles logic for routing requests.


listen 80; # Port on which nginx listens to for HTTP requests

  
server_name localhost;  

# Defines what to do when the `/` location is accessed
location / { 

  

proxy_pass http://spring_cluster; # forwards the request to the cluster

proxy_set_header Host $host; # passes the Host header to the upstream server

  

proxy_set_header X-Real-IP $remote_addr; # passes client IP address to server.

}

}

}
```

Test config file using:
```bash
nginx -t
```

Reload `nginx` to apply new config :
```bash
nginx -s reload
```

Access your application via the NGINX load balancer at `http://localhost:80`. Log the app name in the Spring Boot app to confirm that resources are being distributed.