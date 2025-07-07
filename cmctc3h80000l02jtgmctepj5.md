---
title: "Docker Basics & Advanced Challenge – Week 5 of #90DaysOfDevOps"
seoTitle: "docker , docker advanced"
seoDescription: "Docker Basics & Advanced Challenge – Week 5"
datePublished: Mon Jul 07 2025 16:47:14 GMT+0000 (Coordinated Universal Time)
cuid: cmctc3h80000l02jtgmctepj5
slug: docker-basics-and-advanced-challenge-week-5-of-90daysofdevops
tags: docker-basics-and-advanced-challenge-week-5

---

As part of the #90DaysOfDevOps journey, I’ve completed **Week 5**, which was all about diving deep into Docker – from the basics to some advanced concepts like multi-stage builds, Docker networking, and vulnerability analysis using Docker Scout. This blog documents my learnings, the steps I followed, and key insights I gained.

---

## 🔍 Task 1: Introduction & Conceptual Understanding

### 🌐 What is Docker?

Docker is a platform that enables developers to package applications into containers—lightweight, portable, and consistent units of software that include everything needed to run: code, runtime, system tools, libraries, and settings.

### 🧱 Virtualization vs. Containerization

| Virtualization | Containerization |
| --- | --- |
| Runs full OS per VM | Shares OS kernel, lighter weight |
| Heavyweight, slower startup | Lightweight, fast boot time |
| Suitable for multi-tenant environments | Ideal for microservices and CI/CD |

**Why containers?**  
Containers are perfect for building, shipping, and running apps reliably across different environments. They're faster, portable, and ideal for microservices-based architecture.

---

## 🧰 Task 2: Docker file for a Sample App

### 🧪 My Sample App: Python Flask Web Server : Picked up a GitHub sample repo and cloned in the ec2

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751512623465/f8e02e16-3acd-4ad0-9a36-9dd0f271fa8d.png align="center")

### 📝 Dockerfile

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751513213290/8e7c3dad-b2b3-49f5-8a5f-3f621dbeb9be.png align="center")

Note: Correction the line 7 should be COPY . .

### 🔧 Build & Run

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751513749978/0b04f976-472f-4590-a70d-05907aaafe2b.png align="center")

build command:docker build -t laxmi/sample-app:latest .

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751513718347/02a4cbdc-9d1c-4798-9c8c-46e9e95923fb.png align="center")

---

### 🧩Task -3

### Key Terms – The Building Blocks of Docker

* **📸 Image**: This is like a mold or template. It defines what your application and its environment should look like. Once created, it can be replicated anywhere—your laptop, a cloud server, or a CI/CD pipeline.
    
* **📦 Container**: A running instance of the image. You can think of it as a package that’s been assembled and is now active—just like a product ready to be delivered.
    
* **📄 Docker file**: This is your instruction manual. It tells Docker exactly how to build your image: which base system to use, what dependencies to install, and how to run your app.
    
* **💾 Volume**: External storage that stays intact even if the container is removed or restarted. It’s like storing project data in a shared folder outside the machine—safe and reusable.
    
* **🌐 Network**: Allows containers to communicate with each other securely and efficiently. In microservice architectures, this is critical—just like how different departments in a factory coordinate during production.
    

---

### ⚙️ Docker Components – The Core System

* **⚙️ Docker Engine**: The runtime that does all the heavy lifting. It manages images, containers, and the underlying system resources needed to keep them running smoothly.
    
* **🧑‍💻 Docker CLI**: Your command-line tool to interact with Docker. Whether you're building images, running containers, or checking logs—this is your control panel.
    
* **☁️ Docker Hub**: A cloud-based registry where you can publish your images for others to use or pull existing images created by the community. Think of it as GitHub, but for container images.
    

---

## 🏗️ Task 4: Multi-Stage Build Optimization

### 🧪 Modified Dockerfile

```plaintext
 Builder stage
FROM python:3.9 as builder
WORKDIR /app
COPY . .
RUN pip install --no-cache-dir -r requirements.txt --target=/app/deps


# Final stage
FROM python:3.9-slim
WORKDIR /app
COPY --from=builder /app/deps /app/deps
COPY --from=builder /app .
EXPOSE 80
CMD ["python", "app.py"]
```

### 📏 Size Comparison

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751514753051/6ce457dc-1b0a-4286-8212-7b48276aa160.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751514795634/5bdcd11e-aa3d-42c7-9c57-2b1e6dd7e67d.png align="center")

| Build Type | Size |
| --- | --- |
| Initial Build | ~137 MB |
| Multi-Stage | ~63 MB |

**✅ Insight:** Multi-stage builds reduce bloat and remove unnecessary build-time dependencies.

---

## 📦 Task 5: Docker Hub Integration

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751515337096/2ba83e63-61a8-4c07-8072-b7007efaee9e.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751515316768/24a8c011-2ea1-43ea-9ebe-43a21b8fc787.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751515372442/0455ffac-b662-423c-b301-33f65951649b.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751515429976/7dc11cfb-c613-4030-9f8d-b1f87354a28b.png align="center")

---

## 💾 Task 6: Docker Volumes

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751891600355/4ffaf960-62da-4948-abcf-268eac32781c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751891664153/3e96e154-80ce-4fd7-b978-1232c2861004.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751891878790/c1c6e081-0f65-41c2-8900-a6c5c72f9123.png align="center")

**Why Volumes?**  
They allow you to persist data across container restarts, making them ideal for databases and stateful services.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751892459916/89d08158-587a-4772-83e5-08f001ed9749.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751892475004/5665708b-9dec-4494-bcbe-d33ee7079dd2.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751892500325/f19c8422-d246-4533-b49b-7892d49933b0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751892513163/61614547-06a6-4ce0-9cc6-9df9de2a8592.png align="center")

---

## 🌐 Task 7: Docker Networking performed for a two-tier flask application

```plaintext
bashCopyEditdocker network create my_network
docker run -d --name sample-app --network my_network laxmi/sample-app:v1.0
docker run -d --name my-db --network my_network -e MYSQL_ROOT_PASSWORD=root mysql:latest
```

**Networking Insight:**  
Containers on the same custom network can communicate using container names—essential for microservices.

created a docker bridge:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751901688431/f0e48bda-d914-4a88-a8c8-c2d9fb0ee2b9.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751901750481/f1df4faa-315a-45f6-9853-6e5b5eb6e6f8.png align="center")

Connected mysql and a flask container to the created network .

---

## 🧩 Task 8: Docker Compose

### 📄 docker-compose.yml

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751906073063/fb61321a-7a7f-436f-bf82-e22927c01098.png align="center")

```plaintext
  flaskapp:
    build:
      context: .
    container_name: flaskapp
    environment:
      MYSQL_USER: admin
      MYSQL_PASSWORD: admin 
      MYSQL_ROOT_PASSWORD: admin
      MYSQL_DATABASE: tws_db
      MYSQL_HOST: mysql
    networks:
      - two_tier
    ports:
      - "5000:5000"
    depends_on:
      mysql:
        condition: service_healthy
    restart: always
```

---

## 🛡️ Task 9: Docker Scout Vulnerability Analysis

```plaintext
 scout cves laxmi/sample-app:v1.0 > scout_report.txt
```

### 🔍 Findings Summary:

* **CVEs Found**: 3 (1 High, 1 Medium, 1 Low)
    
* **Affected Layers**: Flask version dependency
    
* **Recommendations**: Use latest base image & pin package versions.
    

**Takeaway:**  
Docker Scout (or tools like Trivy) give visibility into your image's security posture, essential for production readiness.

---

## 📝 Task 10: Reflection

### 📘 Commands Recap

* `docker build`, `docker run`, `docker volume`, `docker network`, `docker-compose`
    
* `docker scout`, `docker push`, `docker pull`
    

### 💡 Final Thoughts

Docker has revolutionized software development by enabling fast, consistent, and scalable deployments. Its lightweight architecture makes it perfect for microservices, and features like Compose and multi-stage builds make DevOps workflows smoother. But remember: with great power comes great responsibility—secure your images!

---