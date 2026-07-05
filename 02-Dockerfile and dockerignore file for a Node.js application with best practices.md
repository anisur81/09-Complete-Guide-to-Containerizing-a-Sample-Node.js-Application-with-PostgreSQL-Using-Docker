# Here's a comprehensive Dockerfile for a Node.js application with best practices:

## Production-Ready Multi-Stage Dockerfile

```dockerfile

FROM node:18-alpine

WORKDIR /usr/src/app

# Copy package files
COPY package*.json ./

# Install production dependencies
RUN npm ci --omit=dev

# Copy application source
COPY . .

# Create non-root user
RUN addgroup -S nodejs && \
    adduser -S nodeuser -G nodejs

# Set ownership
RUN chown -R nodeuser:nodejs /usr/src/app

USER nodeuser

EXPOSE 3000

CMD ["node", "app.js"]

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

# Create a .dockerignore file to exclude unnecessary files:

```
node_modules
npm-debug.log
.git
.env
.DS_Store
README.md
.gitignore
.nyc_output
coverage
.vscode
.idea
*.log
```

Note: This setup ensures your Node.js application is Dockerized efficiently with optimal security, caching, and performance.

