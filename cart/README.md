# 🛒 Cart — Roboshop

## What is Cart Service?
Handles shopping cart operations.
Stores cart items in Redis for fast access.
Fetches product details from Catalogue service.

## Key Difference from Other Services
Cart does NOT connect to MongoDB!
Uses Redis directly for cart storage.
Also talks to Catalogue API for product info.

## Dependencies
- Redis must be running first!
- Catalogue must be running first!
- No MongoDB needed!

## Environment Variables
REDIS_HOST=redis          ← container name of redis
CATALOGUE_HOST=catalogue  ← container name of catalogue
CATALOGUE_PORT=8080       ← catalogue's port

## Commands
# Build
docker build -t kaladharhub/cart:v1 .

# Run
docker run -d \
  --name cart \
  --network roboshop \
  kaladharhub/cart:v1

# Check logs
docker logs cart