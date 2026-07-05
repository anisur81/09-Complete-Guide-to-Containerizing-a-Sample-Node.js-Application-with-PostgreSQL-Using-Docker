# Here are the steps for running the container and testing the application:
---

**6. Run the Docker Container**

- Create and start a container from the image.
- Map the container port to a host port (e.g., host port 8080 to container port 80).
- Assign a container name (e.g., my-app-container).

```
docker run -d -p 8080:80 --name my-app-container your-app-name:v1.0.0

or

  sudo docker run -d \
  --name portfolio-nodejs \
  -p 3000:3000 \
  -e DB_HOST=172.18.0.17 \
  -e DB_PORT=5432 \
  -e DB_NAME=mydb \
  -e DB_USER=postgres \
  -e DB_PASSWORD=postgres \
  portfolio-nodejs:v1

```

---

**7. Test the Application**

- Access the application through a web browser or API client using the host port (e.g., http://localhost:8080).
- Verify all endpoints and functionality are working as expected.

```
curl http://localhost:8080
```

- Check container logs for any errors during testing:

```
docker logs my-app-container
```
