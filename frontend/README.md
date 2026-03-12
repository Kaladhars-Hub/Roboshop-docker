# 🖥️ Frontend — Roboshop

## What is Frontend?
The user-facing website built with nginx.
Only service exposed to the outside world on port 80!
All other services are internal only.

## How Frontend Routes to Backend
nginx.conf has proxy_pass rules:
/api/catalogue/ → catalogue:8080
/api/user/      → user:8080
/api/cart/      → cart:8080
/api/shipping/  → shipping:8080
/api/payment/   → payment:8080

Browser never talks to backend directly!
Everything goes through Frontend nginx!

## Security Features
- Runs as non-root nginx user
- Only port 80 exposed publicly
- All backend services hidden from internet

## Files
Dockerfile  → build instructions
nginx.conf  → routing configuration
static/     → website HTML, CSS, JS, images

## Commands
# Build
docker build -t kaladharhub-frontend:v1 .

# Run — port 80 exposed publicly!
docker run -d \
  --name frontend \
  --network roboshop \
  -p 80:80 \
  kaladharhub-frontend:v1

# Check logs
docker logs frontend