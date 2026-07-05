# Here are the steps for building and verifying the Docker image:

---

**4. Build the Docker Image**

- Build the Docker image using the Dockerfile in the current directory.
- Tag the image with a version number (e.g., v1.0.0).

```
docker build -t your-app-name:v1.0.0 .
```

---

**5. Verify the Image**

- List all Docker images available locally.
- Confirm that the newly built image appears in the list with the correct tag and size.

```
docker images
```

- Optionally, verify the image details:

```
docker inspect your-app-name:v1.0.0
```
