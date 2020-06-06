# Docker Postgres

A barebones example of using Docker to host a Postgres database management server.

## Steps

### 1. Download [Docker](https://www.docker.com/get-started)

The setup process can be different depending on your operating system and its version. Read the instructions carefully.

### 2. Learn [Docker fundamentals](https://www.youtube.com/watch?v=fqMOX6JJhGo) (2hrs + Optional)

Having a good foundation will make us feel much more confident with using Docker.

### 3. Pull Postgres Image from [Dockerhub](https://hub.docker.com/_/postgres)

`docker pull postgres`

### 4. Create a volume directory for Postgres data to persist

This will make sure that data perists between created and deleted Postgres containers.

(ubuntu) `mkdir -p ~/docker/volumes/postgres`

### 5. Build the image and run the container

```bash
docker run \
  --name docker-postgres \
  -d \
  --rm \
  -p 5432:5432 \
  -v ~/docker/volumes/postgres:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=password \
  postgres
```

- `--name`: Gives the created container a specific name besides the random one.
- `-d`: Run container in background so we get control of our terminal back.
- `--rm`: Remove container after we stop it. [Stackoverflow answer why.](https://stackoverflow.com/a/49726371/7933478)
- `-p`: Publish container's ports to host pc. Container's port 5432 becomes our computer's 5432.
- `-v`: Map our directory to the container's. Even if container is removed, data persists on our file system.
- `-e`: Set an environment variable in the container. Given a new empty volume, the postgres image requires env var `POSTGRES_PASSWORD` to be set on first run to configure postgres super user's password.
- `postgres`: Name of image to use, we pulled it earlier.
