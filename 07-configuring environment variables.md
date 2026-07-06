# Here are the steps for configuring environment variables:

---

** Configure Environment Variables**

- Set environment variables directly in the run command for database connection details, API keys, JWT secrets, or application environment (development, production).

```
docker run -d -p 8080:80 --name my-app-container \
  -e DB_HOST=localhost \
  -e DB_USER=admin \
  -e DB_PASSWORD=secret \
  -e API_KEY=abc123 \
  -e JWT_SECRET=your-secret-key \
  -e APP_ENV=production \
  your-app-name:v1.0.0
```

- Alternatively, use an environment file (.env) to store multiple variables.

```
docker run -d -p 8080:80 --name my-app-container --env-file .env your-app-name:v1.0.0
```

- Verify environment variables are set correctly inside the container.

```
docker exec my-app-container env
```

- Inspect a specific variable.

```
docker exec my-app-container printenv APP_ENV
```

- Update environment variables by stopping, removing, and recreating the container with new values.

```
docker stop my-app-container
docker rm my-app-container
docker run -d -p 8080:80 --name my-app-container -e APP_ENV=development your-app-name:v1.0.0
```
