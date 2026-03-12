# 👤 User — Roboshop

## What is User Service?
Handles all user related operations like registration and login.
Built with Node.js.

## Dependencies
- MongoDB → stores user data
- Redis → stores user sessions (login state)
- Both must be running before starting User service!

## Key Difference from Catalogue
Catalogue connects to MongoDB only.
User connects to BOTH MongoDB and Redis!
Redis stores session data so user stays logged in.

## Environment Variables
MONGO=true
MONGO_URL=mongodb://mongodb:27017/users  ← users database
REDIS_URL=redis://redis:6379             ← session storage

## Commands
# Build
docker build -t kaladharhub/user:v1 .

# Run
docker run -d \
  --name user \
  --network roboshop \
  kaladharhub/user:v1

# Check logs
docker logs user