# Complete Guide to Dockerizing a Node.js Application with PostgreSQL

## Prerequisites
- Docker and Docker Compose installed
- Node.js installed (for local testing)
- Basic understanding of Node.js and PostgreSQL

## Project Structure
```
my-app/
├── docker-compose.yml
├── Dockerfile
├── .dockerignore
├── package.json
├── package-lock.json
├── server.js
├── .env
└── src/
    └── models/
        └── index.js
```

## Step 1: Create Sample Node.js Application
Source Code is available on github
```

## Step 2: Create Docker Configuration

### 2.1 Create Dockerfile
```dockerfile
# Use official Node.js image
FROM node:18-alpine

# Set working directory
WORKDIR /usr/src/app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy application source code
COPY . .

# Create a non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001 && \
    chown -R nodejs:nodejs /usr/src/app

# Switch to non-root user
USER nodejs

# Expose the port
EXPOSE 3000

# Start the application
CMD ["node", "server.js"]
```

### 2.2 Create .dockerignore
```
node_modules
npm-debug.log
.env
.git
.gitignore
README.md
.vscode
.idea
```

### 2.3 Create docker-compose.yml
```yaml
version: '3.8'

services:
  # Node.js application
  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: node-app
    restart: unless-stopped
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - PORT=3000
      - DB_HOST=db
      - DB_PORT=5432
      - DB_NAME=mydb
      - DB_USER=postgres
      - DB_PASSWORD=postgres
    depends_on:
      - db
    networks:
      - app-network
    volumes:
      - ./:/usr/src/app
      - /usr/src/app/node_modules
    command: npm run dev  # Use nodemon for development

  # PostgreSQL database
  db:
    image: postgres:15-alpine
    container_name: postgres-db
    restart: unless-stopped
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
      - POSTGRES_DB=mydb
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Optional: pgAdmin for database management
  pgadmin:
    image: dpage/pgadmin4:latest
    container_name: pgadmin
    restart: unless-stopped
    environment:
      - PGADMIN_DEFAULT_EMAIL=admin@example.com
      - PGADMIN_DEFAULT_PASSWORD=admin
    ports:
      - "5050:80"
    depends_on:
      - db
    networks:
      - app-network
    volumes:
      - pgadmin_data:/var/lib/pgadmin

volumes:
  postgres_data:
  pgadmin_data:

networks:
  app-network:
    driver: bridge
```
 
## Step 3: Environment Configuration

 

### 3.2 Create .env.production (for production)
```env
# Application
PORT=3000
NODE_ENV=production

# Database
DB_HOST=db
DB_PORT=5432
DB_NAME=mydb
DB_USER=postgres
DB_PASSWORD=postgres
```

## Step 4: Build and Run

### 4.1 Build and Start Containers
```bash
# Build and start all services
docker-compose up -d

# Build without cache
docker-compose build --no-cache

# Start specific service
docker-compose up -d app
```

### 4.2 Verify Everything is Running
```bash
# Check container status
docker-compose ps

# View logs
docker-compose logs -f app
docker-compose logs -f db

# Check health
curl http://localhost:3000/health
```

### 4.3 Test the Application

#### Test endpoints:
```bash
# Get all users
curl http://localhost:3000/api/users

# Create a new user
curl -X POST http://localhost:3000/api/users \
  -H "Content-Type: application/json" \
  -d '{"name": "Alice Johnson", "email": "alice@example.com"}'

# Get user by ID
curl http://localhost:3000/api/users/1
```

## Step 5: Common Docker Commands

### Manage Containers
```bash
# Start containers
docker-compose up -d

# Stop containers
docker-compose down

# Stop and remove volumes (delete database data)
docker-compose down -v

# Restart containers
docker-compose restart

# View logs
docker-compose logs -f [service-name]

# Execute command inside container
docker exec -it node-app sh

# Access PostgreSQL directly
docker exec -it postgres-db psql -U postgres -d mydb
```

### Development Workflow
```bash
# Rebuild and start after changes
docker-compose up -d --build

# Scale application (if using multiple instances)
docker-compose up -d --scale app=3
```

## Step 6: Production Optimization

### 6.1 Production Dockerfile
```dockerfile
# Production Dockerfile
FROM node:18-alpine

WORKDIR /usr/src/app

# Copy package files
COPY package*.json ./

# Install production dependencies only
RUN npm ci --only=production

# Copy source code
COPY . .

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001 && \
    chown -R nodejs:nodejs /usr/src/app

USER nodejs

EXPOSE 3000

# Use production command
CMD ["node", "server.js"]
```

### 6.2 Production docker-compose.yml
```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.prod
    container_name: node-app-prod
    restart: always
    environment:
      - NODE_ENV=production
      - DB_HOST=db
      - DB_NAME=mydb
      - DB_USER=postgres
      - DB_PASSWORD=postgres
    depends_on:
      db:
        condition: service_healthy
    networks:
      - app-network
    # Add load balancer if needed

  db:
    image: postgres:15-alpine
    container_name: postgres-db-prod
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=${DB_PASSWORD}
      - POSTGRES_DB=mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 30s
      timeout: 10s
      retries: 5

  # Optional: Nginx as reverse proxy
  nginx:
    image: nginx:alpine
    container_name: nginx-proxy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - app
    networks:
      - app-network

volumes:
  postgres_data:

networks:
  app-network:
    driver: bridge
```

## Step 7: Advanced Setup - Multi-Stage Build

```dockerfile
# Multi-stage Dockerfile
# Stage 1: Build
FROM node:18-alpine AS builder

WORKDIR /usr/src/app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# Stage 2: Production
FROM node:18-alpine

WORKDIR /usr/src/app

COPY --from=builder /usr/src/app/node_modules ./node_modules
COPY --from=builder /usr/src/app/dist ./dist
COPY package*.json ./

EXPOSE 3000

CMD ["node", "dist/server.js"]
```

## Step 8: Troubleshooting

### Common Issues and Solutions:

1. **Connection refused between containers**
   - Ensure services are on the same network
   - Check hostname in connection string (use service name, not localhost)

2. **Database not ready when app starts**
   - Use healthcheck in docker-compose
   - Implement retry logic in application

3. **Permission denied**
   - Use non-root user in container
   - Check volume permissions

4. **Port conflicts**
   - Change host port mapping (e.g., "3001:3000")

### Debugging Commands:
```bash
# Check container logs
docker logs -f container_name

# Inspect network
docker network inspect app-network

# Check container IP
docker inspect container_name | grep IPAddress

# Execute interactive shell
docker exec -it container_name /bin/sh

# Check running processes
docker top container_name
```

## Step 9: Monitoring and Logging

### Add Winston Logger (optional)
```bash
npm install winston
```

```javascript
// logger.js
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.Console({
      format: winston.format.simple()
    }),
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

module.exports = logger;
```

## Step 10: Security Best Practices

1. **Use secrets for sensitive data**
```yaml
# docker-compose.yml
secrets:
  db_password:
    file: ./secrets/db_password.txt

services:
  app:
    secrets:
      - db_password
```

2. **Limit container resources**
```yaml
services:
  app:
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M
```

3. **Use read-only root filesystem**
```yaml
services:
  app:
    read_only: true
    tmpfs:
      - /tmp
```

## Conclusion

This guide provides a comprehensive setup for Dockerizing a Node.js application with PostgreSQL. Key takeaways:

- Use Docker Compose for multi-container orchestration
- Implement proper health checks and retry logic
- Use environment variables for configuration
- Follow security best practices
- Optimize for production with multi-stage builds

The complete setup provides:
- ✅ Node.js application with Express
- ✅ PostgreSQL database with persistent storage
- ✅ Environment-based configuration
- ✅ Health checks and automatic recovery
- ✅ Development and production configurations
- ✅ Monitoring and logging capabilities
- ✅ Security best practices

Remember to adjust configuration based on your specific requirements and always use environment-specific configurations for different deployment stages.
