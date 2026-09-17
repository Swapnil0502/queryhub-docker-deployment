# QueryHub — Dockerized Microservices Deployment

QueryHub is a Quora-like Question & Answer platform built using a microservices architecture.

This repository contains the **Docker Compose deployment setup** for running the complete QueryHub application using pre-built Docker images.

The application services are packaged as Docker images and can be pulled from Docker Hub, allowing the project to be deployed without cloning the application source-code repositories.

---

## 🏗️ Architecture

```text
                        ┌──────────────────┐
                        │     Frontend     │
                        │   React + Vite   │
                        │     Nginx        │
                        └────────┬─────────┘
                                 │
                                 ▼
                        ┌──────────────────┐
                        │   API Gateway    │
                        │ Spring Cloud     │
                        │     Gateway      │
                        └────────┬─────────┘
                                 │
                    ┌────────────┴────────────┐
                    │                         │
                    ▼                         ▼
           ┌────────────────┐       ┌─────────────────┐
           │  Auth Service  │       │ Question Service│
           │ Spring Boot    │       │   Spring Boot   │
           └───────┬────────┘       └───────┬─────────┘
                   │                        │
                   ▼                        ├──────────────► MongoDB
                MySQL                      │
                                            ├──────────────► Kafka
                                            │
                                            └──────────────► Elasticsearch

                         ┌──────────────────┐
                         │ Eureka Registry  │
                         │ Service Discovery│
                         └──────────────────┘
```

---

## 🚀 Technologies

### Application

* Java
* Spring Boot
* Spring Cloud Gateway
* Spring Cloud Netflix Eureka
* Spring Data JPA
* Spring Data MongoDB
* Spring Kafka
* Spring Data Elasticsearch
* JWT Authentication
* React
* Vite
* Nginx

### Infrastructure

* Docker
* Docker Compose
* MySQL
* MongoDB
* Apache Kafka
* Elasticsearch
* Eureka Service Registry

---

## 📦 Services

| Service          |    Port | Technology            |
| ---------------- | ------: | --------------------- |
| Frontend         |    `80` | React + Vite + Nginx  |
| API Gateway      |  `8080` | Spring Cloud Gateway  |
| Auth Service     |  `8081` | Spring Boot + MySQL   |
| Question Service |  `8082` | Spring Boot + MongoDB |
| Eureka           |  `8761` | Spring Cloud Eureka   |
| MySQL            |  `3306` | MySQL 8               |
| MongoDB          | `27017` | MongoDB               |
| Kafka            |  `9092` | Apache Kafka          |
| Elasticsearch    |  `9200` | Elasticsearch 8       |

---

## 🐳 Docker Images

The application services are distributed as Docker images.

```text
queryhub-eureka:1.0
queryhub-auth:1.0
queryhub-question:1.0
queryhub-gateway:1.0
queryhub-frontend:1.0
```

The final deployment configuration uses the corresponding Docker Hub images.

---

## 📁 Repository Structure

```text
queryhub-docker-deployment/
│
├── docker-compose.yml
├── .env.example
├── .gitignore
├── README.md
│
├── mysql/
│   └── init/
│       └── 01-schema.sql
│
└── mongo/
    └── init/
        └── seed.js
```

---

## ⚙️ Environment Configuration

Create a `.env` file in the project root.

```bash
cp .env.example .env
```

Example:

```env
MYSQL_ROOT_PASSWORD=change-me
MYSQL_DATABASE=auth

JWT_SECRET=change-me-to-a-long-random-secret
JWT_EXPIRATION=86400000
```

> **Important:** Never commit `.env` to Git. It may contain database passwords and JWT secrets.

---

## ▶️ Run the Application

Make sure Docker and Docker Compose are installed.

Clone the deployment repository:

```bash
git clone https://github.com/Swapnil0502/queryhub-docker-deployment
cd queryhub-docker-deployment
```

Create your environment file:

```bash
cp .env.example .env
```

Update `.env` with your own values.

Start the complete application:

```bash
docker compose up -d
```

Check running containers:

```bash
docker compose ps
```

View logs:

```bash
docker compose logs -f
```

View logs for a specific service:

```bash
docker compose logs -f gateway
```

---

## 🌐 Access the Application

Once all services are running:

### Frontend

```text
http://localhost
```

### API Gateway

```text
http://localhost:8080
```

### Eureka Dashboard

```text
http://localhost:8761
```

### Elasticsearch

```text
http://localhost:9200
```

The frontend communicates with the backend through the API Gateway.

```text
Browser
   │
   ▼
Frontend :80
   │
   ▼
API Gateway :8080
   │
   ├── Auth Service :8081
   │
   └── Question Service :8082
```

---

## 🔎 Service Discovery

QueryHub uses **Eureka** for service discovery.

The services register themselves with Eureka:

```text
Auth Service
      │
      ├──────────► Eureka
      │
Question Service
      │
      └──────────► Eureka
```

The API Gateway uses Eureka service discovery to route requests:

```text
lb://AUTH-SERVICE
lb://QUESTION-SERVICE
```

This means the Gateway does not need to depend on fixed container IP addresses.

---

## 🗄️ Databases

### MySQL

MySQL is used by the Auth Service.

The database is persisted using a Docker volume:

```text
mysql-data
```

### MongoDB

MongoDB is used by the Question Service.

Data is persisted using:

```text
mongo-data
```

### Kafka

Kafka is used for asynchronous event processing such as question view-count events.

Kafka data is persisted using:

```text
kafka-data
```

### Elasticsearch

Elasticsearch is used for question search.

Data is persisted using:

```text
elasticsearch-data
```

---

## 💾 Docker Volumes

The Compose configuration creates persistent volumes:

```text
mysql-data
mongo-data
kafka-data
elasticsearch-data
```

This means removing containers does not automatically remove the stored application data.

For example:

```bash
docker compose down
```

stops and removes the containers while keeping the volumes.

To remove containers **and all associated volumes**:

```bash
docker compose down -v
```

> Use `docker compose down -v` carefully because it deletes the persisted database and service data.

---

## 🔄 Rebuild / Update Services

If a newer Docker image is available:

```bash
docker compose pull
```

Then recreate the containers:

```bash
docker compose up -d
```

To stop the application:

```bash
docker compose down
```

---

## 🧪 Verify Services

Check the containers:

```bash
docker compose ps
```

Check Eureka:

```text
http://localhost:8761
```

Check Elasticsearch:

```text
http://localhost:9200
```

Check Gateway health if Actuator is enabled:

```bash
curl http://localhost:8080/actuator/health
```

---

## 🛠️ Useful Docker Commands

List running containers:

```bash
docker ps
```

List all containers:

```bash
docker ps -a
```

View images:

```bash
docker images
```

View logs:

```bash
docker logs queryhub-gateway
```

Follow logs:

```bash
docker logs -f queryhub-gateway
```

Enter a running container:

```bash
docker exec -it queryhub-gateway sh
```

Stop all Compose services:

```bash
docker compose stop
```

Remove Compose containers:

```bash
docker compose down
```

---

## 🔐 Security Notes

The `.env` file is intentionally excluded from Git.

Do not commit:

```text
.env
```

Do not publish real:

* MySQL passwords
* JWT secrets
* API keys
* Cloud credentials

For production deployment, additional security should be configured for infrastructure services.

In particular:

* Elasticsearch should not be publicly exposed without appropriate security.
* Kafka should not be publicly exposed unless required.
* Database ports should generally remain private.
* Strong production secrets should be generated instead of using development values.
* HTTPS should be configured for public traffic.

---

## ☁️ Deployment

This repository is designed so that the application can eventually be deployed to a cloud VM such as **AWS EC2**.

The deployment flow is:

```text
Developer
    │
    ├── Build application
    │
    ▼
Docker Images
    │
    ▼
Docker Hub
    │
    ▼
GitHub Deployment Repository
    │
    │ docker-compose.yml
    ▼
AWS EC2
    │
    ▼
Docker Compose
    │
    ├── Frontend
    ├── API Gateway
    ├── Auth Service
    ├── Question Service
    ├── Eureka
    ├── MySQL
    ├── MongoDB
    ├── Kafka
    └── Elasticsearch
```

The deployment server does not need the application source code when the required application images are available from Docker Hub.

---

## 🎯 Project Goals

This deployment project demonstrates:

* Containerizing Spring Boot microservices
* Containerizing a React frontend
* Docker image management
* Docker Hub image distribution
* Multi-container application deployment
* Docker Compose orchestration
* Service discovery with Eureka
* Database containers with persistent volumes
* Kafka-based asynchronous processing
* Elasticsearch integration
* Environment-based configuration
* Microservices deployment preparation for AWS

---

## 👨‍💻 Author

**Swapnil**

QueryHub — A personal Quora-like Question & Answer platform built with Java Spring Boot microservices and React.
