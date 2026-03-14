# 🛒 Roboshop — Microservices on Docker

A fully containerised e-commerce application built with **10 microservices** across multiple technology stacks. Built as a hands-on DevOps project to demonstrate Docker skills in a real-world multi-service architecture.

> Live project · All services running via Docker Compose · Data persistence with named volumes

---

## 🏗️ Architecture Overview

```
                          ┌─────────────┐
                          │   Frontend  │  ← nginx (port 80)
                          │   (nginx)   │     reverse proxy
                          └──────┬──────┘
                                 │
          ┌──────────────────────┼──────────────────────┐
          │                      │                      │
   ┌──────▼──────┐        ┌──────▼──────┐        ┌──────▼──────┐
   │  Catalogue  │        │    Cart     │        │    User     │
   │  (Node.js)  │        │  (Node.js)  │        │  (Node.js)  │
   └──────┬──────┘        └──────┬──────┘        └──────┬──────┘
          │                      │                      │
   ┌──────▼──────┐        ┌──────▼──────┐       ┌──────▼──────┐
   │   MongoDB   │        │    Redis    │        │  Shipping   │
   │  (database) │        │   (cache)   │        │   (Java)    │
   └─────────────┘        └─────────────┘        └──────┬──────┘
                                                         │
                   ┌──────────────┐             ┌────────▼──────┐
                   │   Payment    │             │     MySQL     │
                   │   (Python)   │             │  (database)   │
                   └──────┬───────┘             └───────────────┘
                          │
                   ┌──────▼───────┐
                   │   RabbitMQ   │
                   │ (msg queue)  │
                   └──────────────┘
```

---

## 🧩 Services

| Service | Technology | Port | Description |
|---|---|---|---|
| **frontend** | nginx | 80 | Reverse proxy + static UI — entry point for all traffic |
| **catalogue** | Node.js | 8080 | Product listings and details — reads from MongoDB |
| **user** | Node.js | 8080 | User registration, login, profile — MongoDB + Redis |
| **cart** | Node.js | 8080 | Shopping cart — Redis for session, Catalogue for prices |
| **shipping** | Java / Maven | 8080 | Delivery cost calculator — reads from MySQL |
| **payment** | Python / uwsgi | 8080 | Order processing — publishes to RabbitMQ |
| **mongodb** | MongoDB 7.0 | 27017 | Product and user data store |
| **redis** | Redis 7.0 | 6379 | Session cache and cart storage |
| **mysql** | MySQL 8.0 | 3306 | Shipping cities and cost data |
| **rabbitmq** | RabbitMQ 3.13 | 5672 | Async message queue for payment events |

---

## ⚙️ Docker Highlights

### Multi-stage builds
All Node.js and Java services use multi-stage builds to keep images lean.

```dockerfile
# Stage 1 — install dependencies (heavy, temporary)
FROM node:20.19.5-alpine3.21 AS build
WORKDIR /opt/server
COPY package.json .
RUN npm install

# Stage 2 — production image (clean and small)
FROM node:20.19.5-alpine3.21
COPY --from=build /opt/server .
COPY *.js .
```

Result: image sizes reduced by ~80% compared to single-stage builds.

### Non-root user (security best practice)
All custom services run as a dedicated `roboshop` user — never as root.

```dockerfile
RUN addgroup -S roboshop && adduser -S roboshop -G roboshop
USER roboshop
```

### Custom bridge network with DNS
All services communicate by container name — no hardcoded IPs.

```yaml
networks:
  default:
    driver: bridge
    name: roboshop
```

```
catalogue → connects to → mongodb:27017   ✅  (name-based DNS)
user      → connects to → redis:6379      ✅  (name-based DNS)
```

### Named volumes for data persistence
Stateful services (databases) use named volumes so data survives container restarts.

```yaml
volumes:
  mongodb:
  redis:
  mysql:
  rabbitmq:
```

---

## 🚀 How to Run

### Prerequisites
- Docker installed on your machine
- Docker Compose v2+
- Ports 80 open on your server / EC2 security group

### Clone and start
```bash
git clone https://github.com/Kaladhars-Hub/roboshop-docker.git
cd roboshop-docker

# Start all 10 services
docker compose up -d

# Check all services are running
docker compose ps
```

### Verify it's working
```bash
# Check logs of any service
docker compose logs catalogue --tail 20

# Open the website
# http://<your-server-ip>
```

### Stop everything
```bash
docker compose down          # stops containers, keeps data
docker compose down -v       # stops containers AND deletes data
```

---

## 📁 Project Structure

```
roboshop-docker/
├── docker-compose.yml       ← orchestrates all 10 services
│
├── mongodb/
│   └── Dockerfile
├── catalogue/
│   └── Dockerfile
├── redis/
│   └── Dockerfile
├── user/
│   └── Dockerfile
├── cart/
│   └── Dockerfile
├── shipping/
│   └── Dockerfile
├── mysql/
│   └── Dockerfile
├── payment/
│   └── Dockerfile
├── rabbitmq/
│   └── Dockerfile
└── frontend/
    └── Dockerfile
```

---

## 🔧 Troubleshooting

**Service not starting?**
```bash
docker compose ps                         # which service is down?
docker compose logs <service> --tail 30   # what is the error?
```

**Network conflict?**
```bash
docker network rm roboshop
docker compose up -d
```

**No space on disk?**
```bash
docker system prune -a -f     # clean unused images and containers
docker system df              # check Docker disk usage
```

---

## 📚 What I Learned

- Containerising applications across **5 different tech stacks** (Node.js, Java, Python, nginx, databases)
- **Multi-stage builds** to reduce image sizes by ~80%
- **Docker networking** — custom bridge network with DNS-based service discovery
- **Docker Compose** — dependency ordering, environment variables, named volumes
- **Data persistence** — stateful vs stateless service design
- **Security** — non-root containers, no hardcoded secrets
- **Troubleshooting** — reading logs, identifying root causes, fixing real errors

---

## 🛠️ Tech Stack

![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=nodedotjs&logoColor=white)
![Java](https://img.shields.io/badge/Java-ED8B00?style=flat&logo=openjdk&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat&logo=mongodb&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat&logo=mysql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat&logo=redis&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/RabbitMQ-FF6600?style=flat&logo=rabbitmq&logoColor=white)
![nginx](https://img.shields.io/badge/nginx-009639?style=flat&logo=nginx&logoColor=white)
![AWS](https://img.shields.io/badge/AWS_EC2-FF9900?style=flat&logo=amazonaws&logoColor=white)

---

## 👤 Author

**Kaladhar** · [GitHub](https://github.com/Kaladhars-Hub) · [LinkedIn](https://www.linkedin.com/in/kaladharknights)

> Built as part of a hands-on DevOps learning journey.
> Next: deploying this same application on **Kubernetes (EKS)**.
