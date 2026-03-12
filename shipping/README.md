# 🚚 Shipping — Roboshop

## What is Shipping Service?
Handles all shipping and delivery calculations.
Built with Java unlike Node.js and Python services.

## Key Differences from Other Services
Language      → Java
Build tool    → Maven (not npm or pip)
Executable    → .jar file (not server.js or .py)
Web server    → embedded in jar (not uwsgi or node)
Dependencies  → pom.xml (not package.json)

## Multi-Stage Build
Stage 1 (builder):
- Uses full Maven image to compile Java code
- mvn clean package compiles src into .jar file
- Heavy Maven dependencies stay in Stage 1!

Stage 2 (runtime):
- Uses lightweight JRE alpine image
- JRE = only runs Java, cannot compile
- Copies only shipping.jar from Stage 1
- Much smaller final image!

## Why sleep 30?
ENTRYPOINT has sleep 30 before starting!
MySQL needs time to fully initialise first.
Shipping waits 30 seconds then connects to MySQL.

## Dependencies
- Cart must be running first!
- MySQL must be running first!

## Environment Variables
CART_ENDPOINT=cart:8080  ← cart container name
DB_HOST=mysql            ← mysql container name

## Commands
# Build (takes longer — Java compilation!)
docker build -t kaladharhub/shipping:v1 .

# Run
docker run -d \
  --name shipping \
  --network roboshop \
  kaladharhub/shipping:v1

# Check logs
docker logs shipping
```