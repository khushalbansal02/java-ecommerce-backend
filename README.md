# E-Commerce Microservices Platform

A production-ready microservices architecture built with Spring Boot, featuring Redis caching, Kong API Gateway, Apache Kafka messaging, and MongoDB database.

## 🏗️ Architecture Overview

This is a multi-module Spring Boot microservices project demonstrating:
- **Microservices Communication**: REST APIs between services
- **Caching**: Redis for product data optimization
- **API Gateway**: Kong for centralized routing and authentication
- **Async Messaging**: Kafka for order confirmation notifications
- **Database**: MongoDB for flexible document storage

![Architecture Diagram](https://miro.medium.com/v2/resize:fit:828/format:webp/1*epet8HYZIJ9M4fb_3HV1Hg.jpeg)

## 🛠️ Technology Stack

| Component | Technology | Port |
|-----------|-----------|------|
| **Framework** | Spring Boot 3.2.2 | - |
| **Language** | Java 17 | - |
| **Build Tool** | Maven (Multi-module) | - |
| **Database** | MongoDB 7.0 | 27017 |
| **Cache** | Redis 7 | 6379 |
| **Message Broker** | Apache Kafka 7.5 | 9092 |
| **API Gateway** | Kong 3.5 | 8000, 8001 |
| **API Documentation** | SpringDoc OpenAPI 3 | /swagger-ui |

## 📦 Microservices

| Service | Port | Description | Key Features |
|---------|------|-------------|--------------|
| **user-service** | 8081 | User management | Registration, user listing |
| **product-service** | 8092 | Product catalog | CRUD, Redis caching, category filter |
| **cart-service** | 8083 | Shopping cart | Add/view cart items |
| **order-service** | 8084 | Order processing | Create orders, Kafka producer |
| **mail-service** | 8085 | Email notifications | Kafka consumer for order confirmations |

## 🚀 Quick Start (Automated Setup)

### Prerequisites
- **Docker Desktop** (running)
- **Java 17** (JDK)
- **Maven 3.6+**
- **PowerShell** (Windows)

### One-Command Setup

```powershell
# Clone the repository (if not already done)
git clone <repository-url>
cd springboot-ecommerce-microservices

# Run the automated startup script
.\startup.ps1
```

This script will:
1. Start all Docker containers (MongoDB, Redis, Kafka, Kong)
2. Configure Kong API Gateway with routes and authentication
3. Build all Spring Boot microservices
4. Display instructions to run services

### Manual Service Startup

After running `startup.ps1`, start each microservice in a separate terminal:

```powershell
# Terminal 1 - User Service
mvn spring-boot:run -pl user-service

# Terminal 2 - Product Service
mvn spring-boot:run -pl product-service

# Terminal 3 - Cart Service
mvn spring-boot:run -pl cart-service

# Terminal 4 - Order Service
mvn spring-boot:run -pl order-service

# Terminal 5 - Mail Service
mvn spring-boot:run -pl mail-service
```

## 📋 Manual Setup (Step-by-Step)

### 1. Start Infrastructure with Docker

```powershell
# Start all infrastructure services
docker-compose up -d

# Check container status
docker-compose ps

# View logs
docker-compose logs -f
```

### 2. Configure Kong API Gateway

```powershell
# Run Kong configuration script
.\kong-setup.ps1
```

This creates:
- Services for all 5 microservices
- Routes mapping to service endpoints
- Global Key Authentication plugin
- Consumer with API key: `jXt6zvUjbfGJKYgkwCbdIYUd7EEX1rkr`

### 3. Build Microservices

```powershell
# Build all modules
mvn clean install -DskipTests

# Or build individual services
mvn clean install -pl user-service -DskipTests
```

### 4. Run Microservices

Start each service in a separate terminal window.

## 🌐 API Endpoints

### Direct Access (Without Kong)

| Service | Health Check | Swagger UI |
|---------|--------------|------------|
| User | http://localhost:8081/users/v1/hello | http://localhost:8081/swagger-ui/index.html |
| Product | http://localhost:8092/products/v1/hello | http://localhost:8092/swagger-ui/index.html |
| Cart | http://localhost:8083/carts/v1/hello | http://localhost:8083/swagger-ui/index.html |
| Order | http://localhost:8084/orders/v1/hello | http://localhost:8084/swagger-ui/index.html |
| Mail | http://localhost:8085 | http://localhost:8085/swagger-ui/index.html |

### Through Kong API Gateway

All requests through Kong require the API key header:

```bash
# Example: Get all users through Kong
curl -H "apikey: jXt6zvUjbfGJKYgkwCbdIYUd7EEX1rkr" \
     http://localhost:8000/users/v1/all

# Example: Register a new user
curl -X POST http://localhost:8000/users/v1/register \
     -H "apikey: jXt6zvUjbfGJKYgkwCbdIYUd7EEX1rkr" \
     -H "Content-Type: application/json" \
     -d '{"name":"John","email":"john@example.com"}'
```

### API Key
- **Key**: `jXt6zvUjbfGJKYgkwCbdIYUd7EEX1rkr`
- **Header**: `apikey: <key>`

## 📊 Service Communication Flow

```
User → Kong (8000) → user-service (8081) → MongoDB
                   → product-service (8092) → Redis → MongoDB
                   → cart-service (8083) → MongoDB
                   → order-service (8084) → MongoDB → Kafka
                                                     ↓
                                          mail-service (8085) → Email
```

## 🔧 Configuration

All services support environment variables for Docker deployment:

| Variable | Default | Purpose |
|----------|---------|---------|
| `MONGODB_HOST` | localhost | MongoDB hostname |
| `REDIS_HOST` | localhost | Redis hostname |
| `KAFKA_HOST` | localhost | Kafka hostname |

**Example**: Run with Docker containers
```powershell
$env:MONGODB_HOST="mongodb"; $env:REDIS_HOST="redis"; $env:KAFKA_HOST="kafka"
mvn spring-boot:run -pl product-service
```

## 🐳 Docker Services

### Container Management

```powershell
# Start all containers
docker-compose up -d

# Stop all containers
docker-compose down

# Stop and remove volumes (clean state)
docker-compose down -v

# View logs
docker-compose logs -f [service-name]

# Restart a specific service
docker-compose restart mongodb
```

### Service URLs

| Service | Container | Host Access |
|---------|-----------|-------------|
| MongoDB | mongodb:27017 | localhost:27017 |
| Redis | redis:6379 | localhost:6379 |
| Kafka | kafka:29092 | localhost:9092 |
| Zookeeper | zookeeper:2181 | localhost:2181 |
| Kong Gateway | kong:8000 | localhost:8000 |
| Kong Admin API | kong:8001 | localhost:8001 |
| Kong Manager | kong:8002 | localhost:8002 |

## 🧪 Testing the Application

### 1. Register a User
```bash
curl -X POST http://localhost:8000/users/v1/register \
  -H "apikey: jXt6zvUjbfGJKYgkwCbdIYUd7EEX1rkr" \
  -H "Content-Type: application/json" \
  -d '{"name":"Alice","email":"alice@example.com","address":"123 Main St"}'
```

### 2. Add Products
```bash
curl -X POST http://localhost:8000/products/v1 \
  -H "apikey: jXt6zvUjbfGJKYgkwCbdIYUd7EEX1rkr" \
  -H "Content-Type: application/json" \
  -d '{"name":"Laptop","price":999.99,"category":"Electronics","stock":50}'
```

### 3. Add Items to Cart
```bash
curl -X POST http://localhost:8000/carts/v1 \
  -H "apikey: jXt6zvUjbfGJKYgkwCbdIYUd7EEX1rkr" \
  -H "Content-Type: application/json" \
  -d '{"userId":"<user-id>","items":[{"productId":"<product-id>","quantity":2}]}'
```

### 4. Create Order (Triggers Kafka Event)
```bash
curl -X GET http://localhost:8000/orders/v1/users/<user-id> \
  -H "apikey: jXt6zvUjbfGJKYgkwCbdIYUd7EEX1rkr"
```

The order-service will:
1. Create order from user's cart
2. Publish message to Kafka topic: `order-confirmation-events`
3. Mail-service consumes the event and logs email notification

## 🔍 Monitoring & Debugging

### View Application Logs
```powershell
# Docker container logs
docker-compose logs -f mongodb
docker-compose logs -f kafka

# Spring Boot logs (in terminal where service is running)
```

### Check Kong Configuration
```bash
# List all services
curl http://localhost:8001/services

# List all routes
curl http://localhost:8001/routes

# List consumers
curl http://localhost:8001/consumers
```

### Kafka Topic Verification
```powershell
# Enter Kafka container
docker exec -it ecommerce-kafka bash

# List topics
kafka-topics --list --bootstrap-server localhost:9092

# View messages in topic
kafka-console-consumer --bootstrap-server localhost:9092 \
  --topic order-confirmation-events --from-beginning
```

## 🛑 Troubleshooting

### Services Can't Connect to Docker Infrastructure

If services can't connect to MongoDB/Redis/Kafka:
```powershell
# Set environment variables
$env:MONGODB_HOST="localhost"
$env:REDIS_HOST="localhost"
$env:KAFKA_HOST="localhost"
```

### Kong Returns 404

Ensure services are running and Kong is configured:
```powershell
.\kong-setup.ps1
```

### Port Already in Use

Check what's using the port and stop it:
```powershell
# Find process using port 8081
netstat -ano | findstr :8081

# Kill process by PID
taskkill /PID <PID> /F
```

## 📚 Project Structure

```
springboot-ecommerce-microservices/
├── pom.xml                      # Parent POM
├── docker-compose.yml           # Infrastructure setup
├── startup.ps1                  # Automated startup script
├── kong-setup.ps1              # Kong configuration script
├── user-service/               # User management microservice
├── product-service/            # Product catalog + Redis cache
├── cart-service/               # Shopping cart management
├── order-service/              # Order processing + Kafka producer
└── mail-service/               # Email notifications + Kafka consumer
```

## 🔐 Security

- **Kong Key Authentication**: Global API key required for all routes
- **API Key**: `jXt6zvUjbfGJKYgkwCbdIYUd7EEX1rkr` (stored in user-service config)
- **Production Note**: Use environment variables and secrets management for API keys

## 📖 Additional Resources

- **Medium Article**: [Building beyond the Basics: Crafting Microservices with Spring Boot, Redis, Kong, Kafka, and MongoDB](https://medium.com/@karanbhogle/building-beyond-the-basics-crafting-microservices-with-spring-boot-redis-kong-kafka-and-269d25853e33)
- **Architecture Diagram**: See linked Medium article for detailed component interaction
- **OpenAPI Docs**: Available at `/swagger-ui/index.html` on each service
- **Kong Manager GUI**: http://localhost:8002 (visual Kong administration)

## 📝 License

This project is for educational and demonstration purposes.

---

**Happy Coding! 🚀**
