# Here are the steps for persisting application data using Docker volumes

---

**11. Persist Application Data (Optional)**

- Create a named volume for persistent storage.

```
docker volume create app-data
```

- Run the container with the volume mounted to persist uploaded files, database storage, or application-generated data (e.g., mount to /app/data inside the container).

```
docker run -d -p 8080:80 --name my-app-container -v app-data:/app/data your-app-name:v1.0.0
```

- Alternatively, mount a host directory for persistent data (bind mount).

```
docker run -d -p 8080:80 --name my-app-container -v /host/path:/app/data your-app-name:v1.0.0
```

- Verify the volume is created and attached.

```
docker volume ls
docker inspect my-app-container
```

- To back up or restore volume data, copy files to/from the container.

```
docker cp my-app-container:/app/data ./backup/
```
