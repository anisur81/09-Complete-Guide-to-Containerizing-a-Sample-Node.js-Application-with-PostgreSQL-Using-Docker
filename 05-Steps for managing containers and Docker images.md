# Here are the steps for managing containers and Docker images:

---

##. Manage the Container**

- Stop the container.

```
docker stop my-app-container
```

- Start it again when needed.

```
docker start my-app-container
```

- Restart if necessary.

```
docker restart my-app-container
```

- Remove the container when no longer required.

```
docker rm my-app-container
```

---

**10. Manage Docker Images**

- List available images.

```
docker images
```

- Remove unused images to free disk space.

```
docker image prune
```

- Remove a specific unused image.

```
docker rmi your-app-name:v1.0.0
```

##. Verify the container is running
```

sudo docker ps
For all container

sudo docker ps -a
```
## 8. View application logs
```
sudo docker logs -f portfolio-nodejs
```
