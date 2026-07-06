# Here are the steps for using Docker Compose:

---

**Use Docker Compose (Optional)**

- Create a `docker-compose.yml` file defining all services (application, database, etc.).

```yaml
version: '3.8'

services:
  app:
    image: your-app-name:v1.0.0
    container_name: my-app-container
    ports:
      - "8080:80"
    environment:
      - DB_HOST=database
      - DB_USER=root
      - DB_PASSWORD=rootpassword
      - DB_NAME=myapp
      - APP_ENV=production
    volumes:
      - app-data:/app/data
    depends_on:
      - database
    networks:
      - app-network

  database:
    image: mysql:8.0
    container_name: database
    environment:
      - MYSQL_ROOT_PASSWORD=rootpassword
      - MYSQL_DATABASE=myapp
    volumes:
      - db-data:/var/lib/mysql
    networks:
      - app-network

volumes:
  app-data:
  db-data:

networks:
  app-network:
```

- Start all services with a single command.

```
docker-compose up -d
```

- View the status of all running services.

```
docker-compose ps
```

- View logs from all services.

```
docker-compose logs
```

- View logs from a specific service.

```
docker-compose logs app
```

- Stop all services.

```
docker-compose stop
```

- Start all services again.

```
docker-compose start
```

- Stop and remove all services together.

```
docker-compose down
```

- Stop and remove all services along with volumes.

```
docker-compose down -v
```

- Rebuild and start services after code changes.

```
docker-compose up -d --build
```
