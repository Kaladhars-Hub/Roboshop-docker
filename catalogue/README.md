# 📦 Catalogue — Roboshop

## What is Catalogue?
Catalogue service handles all product related operations.
Built with Node.js, connects to MongoDB for product data.
First API service that Frontend talks to for product listing.

## Folder Structure
```
catalogue
├── Dockerfile          → build instructions
├── .dockerignore       → ignores db folder during build
├── package.json        → node.js dependencies
├── server.js           → main application code
└── db
    └── master-data.js  → local reference only (not copied into image)
```

## Why .dockerignore?
db folder is listed in .dockerignore because:
- Master data is already loaded in MongoDB container
- No need to copy db folder into catalogue image
- Keeps image clean and small!

## Multi-Stage Build Explained
Stage 1 (build stage):
- Uses node:20.19.5-alpine3.21 as base
- Copies package.json and server.js
- Runs npm install to install dependencies
- Creates heavy node_modules folder here

Stage 2 (final stage):
- Fresh clean node:20.19.5-alpine3.21 image
- Copies only built files from Stage 1
- Leaves behind heavy build tools
- Result = smaller cleaner production image!

## Security Features
- Non-root user created (roboshop group and user)
- File ownership given to roboshop user
- Container runs as roboshop not root!

## Dockerfile Instructions Used
- FROM    → node:20.19.5-alpine3.21 (tiny alpine Linux)
- WORKDIR → /opt/server
- COPY    → package.json and server.js files
- RUN     → npm install + create user
- EXPOSE  → 8080
- LABEL   → project metadata
- ENV     → MongoDB connection details
- USER    → roboshop (non-root)
- CMD     → server.js (default argument)
- ENTRYPOINT → node (locked command)

## Environment Variables
```
MONGO=true
MONGO_URL=mongodb://mongodb:27017/catalogue
```
"mongodb" = container name on roboshop network
Docker automatically resolves name to private IP!

## Dependencies
- MongoDB must be running first!
- Must be on roboshop network!

## Commands
# Build image
docker build -t kaladharhub/catalogue:v1 .

# Run on roboshop network
docker run -d \
  --name catalogue \
  --network roboshop \
  kaladharhub/catalogue:v1

# Check running
docker ps

# Check logs
docker logs catalogue

# Go inside container
docker exec -it catalogue sh

## Old Simple Version (before optimisation)
The commented out section at bottom of Dockerfile
shows the original simple version before
multi-stage build was introduced.
This shows the evolution from simple to production-grade!