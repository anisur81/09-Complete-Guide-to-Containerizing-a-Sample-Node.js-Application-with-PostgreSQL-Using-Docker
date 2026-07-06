
# Set Up Docker Own Local Docker Registry: A Step-by-Step Guide


## Setting Up a Local Docker Registry

## Step 1: Install Docker
Ensure Docker is installed on your machine.

## Step 2: Run the Docker Registry Container
Open your terminal and execute the following command to start the Docker Registry in a container:

```bash
$ docker run -d -p 5000:5000 --name local-registry registry:latest
```

Verify the status of the container:

```bash
$ docker container ls
```

## Step 3: Pull, Tag and Push an Image
To use the registry, you'll need to push an image. First, pull an image from Docker Hub or build from locally:

```bash
$ docker pull nginx:latest
```

Tag the image to point to your local registry:

```bash
$ docker tag nginx:latest localhost:5000/nginx:latest
```

Now, push it to your local registry:

```bash
$ docker push localhost:5000/nginx:latest
```

## Step 4: Verify your Local Registry Setup
Log in to the Local Registry container and verify whether the image is present in it:

```bash
docker exec -it local-registry sh
# cd /var/lib/registry/docker/registry/v2/repositories
# ls
nginx
```

Pull from registry:

```bash
# docker pull localhost:5000/nginx:latest
```

If clients (Docker daemon) refuse to pull because registry is "insecure", add on each client (Linux):

```bash
# vi /etc/docker/daemon.json:
{
  "insecure-registries" : ["172.21.1.170:5000"]
}
# systemctl restart docker
```

Create the container from the local registry:

```bash
# docker run -d --name test_registry_02_n2 172.21.1.170:5000/nginx:latest
```

## Step 5: List Images and Tags

To list the images:

```bash
root@dockno1:/home/k8s# curl http://localhost:5000/v2/_catalog
{"repositories":["harbortest/nginx","mysql","nginx"]}
```

To list the tags:

```bash
root@dockno1:/home/k8s# curl http://localhost:5000/v2/harbortest/nginx/tags/list
{"name":"harbortest/nginx","tags":["latest"]}
```

### Using jq for Better Output

```bash
curl -s http://localhost:5000/v2/_catalog | jq -r '.repositories[]'
```
```
