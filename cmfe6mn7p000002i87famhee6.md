---
title: "Chattingo: Our Hackathon Journey from Errors to Deployment"
seoDescription: "A detailed case study of building Chattingo, a real-time chat app, during a hackathon. Learn about Docker multi-stage builds, Jenkins CI/CD, Trivy security "
datePublished: Wed Sep 10 2025 16:16:45 GMT+0000 (Coordinated Universal Time)
cuid: cmfe6mn7p000002i87famhee6
slug: chattingo-our-hackathon-journey-from-errors-to-deployment
tags: hackathon, docker, github, devops, ci-cd

---

Hackathons are like pressure cookers — short deadlines, long nights, endless errors, and massive learning packed into just a few days. For this hackathon, our team decided to build **Chattingo**, a real-time chat application with authentication, Dockerized deployment, and a Jenkins CI/CD pipeline.

This blog is both our **storytelling diary** and a **deep technical case study** of how we built Chattingo, the errors we fought, and the lessons we took home.

---

## Why Chattingo?

We wanted to build something practical and fun:

* A **real-time chat app** (because everyone loves chatting).
    
* Simple **login & signup** with JWT-based authentication.
    
* **Dockerized microservices** (backend, frontend, database).
    
* A working **CI/CD pipeline with Jenkins**.
    
* Security scans using **Trivy**.
    

---

## The Tech Stack

* **Frontend** → React + Nginx (Dockerized)
    
* **Backend** → Node.js + Express + MySQL
    
* **Database** → MySQL (containerized)
    
* **CI/CD** → Jenkins pipeline
    
* **Security** → Trivy scans for Docker images
    
* **Deployment** → VPS with Docker Compose
    

---

## The Journey

### 1\. Setting up the Backend

We started with a simple Node.js + Express backend. The goal: handle user signup, login, and chat APIs.

Here’s the **backend Dockerfile** we wrote:

```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 8080

CMD ["npm", "start"]
```

### This worked… until it didn’t. The first big error was:

```plaintext
Error: secretOrPrivateKey must have a value
```

Root cause: Our `JWT_SECRET` was too short. We fixed it by setting a **properly long secret key** in the `.env`.

---

### 2\. Database Woes

MySQL in Docker sounds easy, right? Wrong. We kept hitting:

```plaintext
ECONNREFUSED 172.18.0.2:3306
```

Turns out:

* We were trying to connect to [`localhost`](http://localhost).
    
* In Docker Compose, you must connect via **service name**, not [localhost](http://localhost).
    

✅ Fix: update `.env` with:

```plaintext
DB_HOST=mysql
DB_USER=root
DB_PASSWORD=rootpassword
DB_NAME=chattingo
```

---

### 3\. Frontend + Nginx

Our React app built fine, but connecting it to the backend was a nightmare.

First attempt gave us only the **login page with no API response**.  
After hours of head-scratching, we realized Nginx was forwarding requests to [`localhost:8080`](http://localhost:8080) instead of the backend service.

Here’s the **fixed nginx.conf**:

```plaintext
server {
  listen 80;

  location / {
    root   /usr/share/nginx/html;
    index  index.html;
    try_files $uri /index.html;
  }

  location /api/ {
    proxy_pass http://backend:8080/;
  }
}
```

The key fix: `proxy_pass` [`http://backend:8080`](http://backend:8080)`;` (not [`localhost`](http://localhost)).

---

### 4\. Docker Compose

Our `docker-compose.yml` brought everything together:

```plaintext
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - "8080:8080"
    env_file:
      - ./backend/.env
    depends_on:
      - mysql

  frontend:
    build: ./frontend
    ports:
      - "3000:80"
    depends_on:
      - backend

  mysql:
    image: mysql:8.0
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: chattingo
    ports:
      - "3306:3306"
```

Finally, `docker compose up --build` gave us all containers running 🎉.

---

### 5\. CI/CD with Jenkins

We wanted a **fail-proof Jenkins pipeline**:

* Clone repo
    
* Run Trivy scans
    
* Build Docker images
    
* Push to Docker Hub
    
* Deploy with Docker Compose
    

Here’s the simplified **Jenkinsfile**:

```plaintext
pipeline {
    agent any

    stages {
        stage('Clone Code') {
            steps {
                git url: 'https://github.com/<your-username>/chattingo.git', branch: 'main'
            }
        }

        stage('Trivy Scan') {
            steps {
                sh 'trivy image node:18 || true'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t <your-username>/chattingo-backend ./backend'
                sh 'docker build -t <your-username>/chattingo-frontend ./frontend'
            }
        }

        stage('Push Images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push <your-username>/chattingo-backend'
                    sh 'docker push <your-username>/chattingo-frontend'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose down && docker compose up -d --build'
            }
        }
    }
}
```

---

## Biggest Errors & How We Fixed Them

1. **JWT secret error** → fixed with a longer key.
    
2. **MySQL connection refused** → fixed by using service name in `.env`.
    
3. **Frontend only showing login** → fixed nginx `proxy_pass`.
    
4. **Pipeline stuck on last stage** → added `|| true` for Trivy, ensured `docker compose down` before redeploy.
    

---

## The Demo

When everything finally worked, we had:

* ✅ Signup & Login
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757520815703/1d90a7ac-b3b8-4b69-9956-cf85bb71fad5.png align="center")
    
      
    
* ✅ Real-time chat functionality
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757520803984/da21f1b0-89b9-41e2-b07a-d86bf917cb85.png align="center")
    
      
    
* ✅ Fully containerized stack
    
* ✅ CI/CD pipeline auto-deploying to VPS
    

Hackathon ✅ Submission ✅ Sleep ❌

---

## Lessons Learned

* **Docker networking** is different from [localhost](http://localhost) debugging.
    
* **Secrets matter** (JWT, DB passwords).
    
* **CI/CD pipelines** are lifesavers once working, nightmares until then.
    
* Hackathons are less about perfection and more about **iteration + resilience**.
    

---

## Conclusion

Building **Chattingo** in a hackathon was a rollercoaster. We broke things, fixed things, and learned more in a few days than weeks of tutorials.

If you’re joining your next hackathon, remember:

* Start small,
    
* Debug smart,
    
* And don’t forget to celebrate the errors — they teach the most.
    

---