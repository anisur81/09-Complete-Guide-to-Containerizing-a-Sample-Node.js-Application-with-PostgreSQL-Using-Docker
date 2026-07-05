# Here's a comprehensive Dockerfile for a Node.js application with best practices:

## Production-Ready Multi-Stage Dockerfile

```dockerfile
# Stage 1: Build stage
FROM node:18-alpine AS builder

WORKDIR /usr/src/app

# Copy package files
COPY package*.json ./

# Install all dependencies (including dev dependencies)
RUN npm ci

# Copy source code
COPY . .

# Build the application
RUN npm run build

# Stage 2: Production stage
FROM node:18-alpine

WORKDIR /usr/src/app

# Copy package files
COPY package*.json ./

# Install only production dependencies
RUN npm ci --only=production

# Copy built application from builder stage
COPY --from=builder /usr/src/app/dist ./dist
COPY --from=builder /usr/src/app/node_modules ./node_modules

# Create non-root user for security
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Change ownership to non-root user
COPY --chown=nodejs:nodejs . .

USER nodejs

# Expose the application port
EXPOSE 3000

# Define the startup command
CMD ["node", "dist/server.js"]
```



## Dockerfile Best Practices Checklist

✅ **Base Image**: Use official, slim images (e.g., `node:18-alpine`)  
✅ **Layer Caching**: Copy `package*.json` before application code  
✅ **Dependencies**: Use `npm ci` instead of `npm install` for reproducibility  
✅ **Security**: Run as non-root user, use `USER node`  
✅ **Build Stage**: Use multi-stage builds for production  
✅ **Port Exposure**: Define the correct port (usually 3000)  
✅ **Healthcheck**: Add for container orchestration  
✅ **Environment**: Set `NODE_ENV=production`  
✅ **Clean Cache**: Remove unnecessary files to reduce image size  
✅ **Explicit CMD**: Use `CMD` for default command, not `ENTRYPOINT`




