# 🍃 MongoDB — Roboshop

## What is MongoDB?
MongoDB is a NoSQL database that stores data 
as JSON-like documents instead of tables.
Used in Roboshop to store product catalogue data.

## Why Official Image?
In production we never build database images from scratch.
We use official images because:
- Pre-configured and optimised
- Security patches maintained
- No need for RUN or CMD instructions
- Official image handles everything!

## Dockerfile Explanation
FROM mongo:7.0
- Uses official MongoDB version 7.0 image
- Same version used in shell script project

COPY *.js /docker-entrypoint-initdb.d
- Copies master-data.js into special MongoDB folder
- MongoDB automatically runs all .js files in this folder
- This loads initial product data on first startup!

## Commands Used
# Build image
docker build -t kaladharshub/mongodb:v1 .

# Run on roboshop network
docker run -d \
  --name mongodb \
  --network roboshop \
  kaladharshub/mongodb:v1

# Verify running
docker ps

# Go inside container
docker exec -it mongodb bash

# Check if data loaded
mongosh
show dbs
use catalogue
db.products.find()

## Key Points
- No CMD needed — official mongo image starts MongoDB automatically
- --name mongodb — other services find it by this name
- --network roboshop — isolated network for all Roboshop services
- In production — databases are NEVER containerised!
  Use AWS RDS or MongoDB Atlas instead.