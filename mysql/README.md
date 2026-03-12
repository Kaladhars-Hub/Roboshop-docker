# 🐬 MySQL — Roboshop

## What is MySQL?
Relational database used by Shipping service.
Stores shipping and delivery related data.

## Files
Dockerfile        → build instructions
custom-script.sh  → handles password and startup
db/               → SQL files loaded on first startup

## Why custom-script.sh?
MySQL password is set inside custom-script.sh
instead of ENV in Dockerfile — more secure!
Script runs on container startup automatically.

## Dependencies
- No other services needed!
- But Shipping connects to MySQL
- So MySQL must start before Shipping works!

## Commands
# Build
docker build -t kaladharhub-mysql:v1 .

# Run
docker run -d \
  --name mysql \
  --network roboshop \
  kaladharhub-mysql:v1

# Check logs
docker logs mysql