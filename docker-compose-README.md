# 🐳 Docker Compose — Roboshop

## What is Docker Compose?
A simple YAML file that manages all 10 Roboshop services together.
One command to start everything, one command to stop everything!

## Why Compose?
Without Compose you need to:
- Run 10 separate docker build commands
- Run 10 separate docker run commands
- Remember correct startup order every time!

With Compose:
```bash
docker compose up -d    # starts all 10 services in correct order!
docker compose down     # stops all 10 services together!
```

## File Structure Explained

### services
Every container is a service. Each service has:
- `build` → which folder has the Dockerfile
- `image` → what to name the built image
- `container_name` → name of the running container
- `depends_on` → which services must start first
- `environment` → environment variables (-e flag)
- `ports` → port mapping (-p flag) — only frontend!
- `volumes` → data persistence (-v flag)

### networks
```yaml
networks:
  default:
    driver: bridge
    name: roboshop
    external: false
```
- Compose creates roboshop network automatically!
- No need to run `docker network create roboshop` manually
- `external: false` = Compose manages it
- `external: true` = you created it manually before running compose

### volumes
```yaml
volumes:
  mongodb:
  redis:
  mysql:
  rabbitmq:
```
- Named volumes — Docker creates and manages them
- Data persists even after `docker compose down`
- Only deleted with `docker compose down -v`
- Stored at `/var/lib/docker/volumes/`

## Service Dependencies (depends_on)

```
mongodb   ← no dependencies (starts first)
redis     ← no dependencies (starts first)
mysql     ← no dependencies (starts first)
rabbitmq  ← no dependencies (starts first)
catalogue ← needs mongodb
user      ← needs mongodb + redis
cart      ← needs redis + catalogue
shipping  ← needs mysql + cart
payment   ← needs rabbitmq + cart + user
frontend  ← needs all services
```

## Stateful vs Stateless

### Stateful Services (need volumes — user data!)
- mongodb → `/data/db`
- redis → `/data`
- mysql → `/var/lib/mysql`
- rabbitmq → `/var/lib/rabbitmq`

### Stateless Services (no volumes — code is in Git!)
- catalogue, user, cart, shipping, payment, frontend

## Commands

```bash
# Start all services
docker compose up -d

# Check all running
docker compose ps

# Check specific service logs
docker compose logs catalogue
docker compose logs shipping

# Follow live logs
docker compose logs -f

# Stop all containers (volumes kept!)
docker compose down

# Stop + delete volumes (fresh start — data gone!)
docker compose down -v

# Stop + delete images too
docker compose down --rmi all

# Restart specific service
docker compose restart catalogue
```

## Troubleshooting

### Network conflict error
```
network roboshop was found but has incorrect label
```
Fix: Remove manually created network first:
```bash
docker network rm roboshop
docker compose up -d
```

### Service not starting
```bash
# Check logs of failed service
docker compose logs <service-name>
```

### Check what's running
```bash
docker compose ps
```

## Important Notes
- Frontend is the ONLY service with ports exposed (80:80)
- All other services communicate internally via container names
- Container names are used as DNS — catalogue connects to mongodb using "mongodb" not IP
- `docker compose down` does NOT delete images or volumes
- Always run `docker compose down` before `docker compose up -d` if changing config
