# 💰 Payment — Roboshop

## What is Payment Service?
Handles all payment processing operations.
Built with Python unlike other Node.js services.

## Key Differences from Node.js Services
Language        → Python (not Node.js)
Package manager → pip3 (not npm)
Web server      → uwsgi (not node)
Config file     → requirements.txt (not package.json)
Start command   → uwsgi --ini payment.ini

## Multi-Stage Build
Stage 1 (builder):
- Installs heavy build tools (build-base, linux-headers)
- Installs Python packages into /install folder
- These build tools are NOT carried to Stage 2!

Stage 2 (runtime):
- Fresh clean Python alpine image
- Only runtime library (pcre) installed
- Copies packages from Stage 1 /install folder
- Much smaller final image!

## Dependencies
- Cart must be running first!
- User must be running first!
- RabbitMQ must be running first!

## Environment Variables
CART_HOST=cart        ← cart container name
CART_PORT=8080
USER_HOST=user        ← user container name
USER_PORT=8080
AMQP_HOST=rabbitmq    ← rabbitmq container name
AMQP_USER=roboshop
AMQP_PASS=roboshop123

## Commands
# Build
docker build -t kaladharhub/payment:v1 .

# Run
docker run -d \
  --name payment \
  --network roboshop \
  kaladharhub/payment:v1

# Check logs
docker logs payment