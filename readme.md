# Docker Postgres

A barebones example of using Docker to host a Postgres database management server.

This approach can be nice so you dont have to worry about configuring your computer to run the database. Just get docker working, learn how to use it, then you can run any database or service from your computer without messing with your own environment.

## Steps

Here are two methods of getting the postgres container running:

1. using the convenient `docker-compose.yml` configuration file
1. using the fast `docker run` command

I suggest reading through the `docker run` method first. It can be very fast for getting small things running.

The `docker-compose.yml` method is good for running containers that take too long to type out as a `docker run`command every time we want to use it.

### Setup, regardless of method used

1. Download [Docker](https://www.docker.com/get-started)

The setup process can be different depending on your operating system and its version. Read the instructions carefully. Persevere!

2. Learn [Docker fundamentals](https://www.youtube.com/watch?v=fqMOX6JJhGo) (2hrs + Optional)

Having a good foundation will make us feel much more confident with using Docker. You dont have to do this now, but get around to it if you want to use docker a lot in the future.

### `docker run` method

1. Pull Postgres Image from [Dockerhub](https://hub.docker.com/_/postgres)

`docker pull postgres`

2. Create a volume directory for Postgres data to persist

This will make sure that data persists between created and deleted Postgres containers.

(ubuntu) `mkdir -p ~/docker/volumes/postgres`

3. Build the image and run the container

```bash
docker run \
  --name docker-postgres \
  --detach \
  --rm \
  --publish 5432:5432 \
  --volume ~/docker/volumes/postgres:/var/lib/postgresql/data \
  --env POSTGRES_PASSWORD=password \
  postgres
```

- `--name`: Gives the created container a specific name besides the random one.
- `--detach`: Run container in background so we get control of our terminal back.
- `--rm`: Remove container after we stop it. [Stackoverflow answer why.](https://stackoverflow.com/a/49726371/7933478)
- `--publish`: Publish container's ports to host pc. Container's port 5432 becomes our computer's 5432.
- `--volume`: Map our directory to the container's. Even if container is removed, data persists on our file system.
- `--env`: Set an environment variable in the container. Given a new empty volume, the postgres image requires env var `POSTGRES_PASSWORD` to be set on first run to configure postgres super user's password.
- `postgres`: Name of image to use, we pulled it earlier.

4. stop the container when you want

Going back to knowing fundamentals, user `docker ps` to list running containers and `docker stop <name>` to stop one.

### `docker-compose.yml` method

1. Create a volume directory for Postgres data to persist

This will make sure that data perists between created and deleted Postgres containers.

(ubuntu) `mkdir -p ~/docker/volumes/postgres`

2. (from directory with `docker-compose.yml` file) `docker-compose up --detach`

This will build and run a container in the background according to the `docker-compose.yml` file. We can see how the `docker run` options port over to the configuration file.

```yml
version: "3.2"
services:
  postgres:
    image: postgres
    ports:
      - 5432:5432
    volumes:
      - type: bind
        source: ~/docker/volumes/postgres
        target: /var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: "password"
```

3. stop the container

Use `docker-compose down` or a mix of `docker ps` to list running containers and `docker stop` to stop one. If your `docker-compose.yml` eventually starts up several services or containers, using `docker-compose down` will be much more convenient as it takes them all down at once.

## Closing Statements

I personally learned how to do this from [this tutoral](https://hackernoon.com/dont-install-postgres-docker-pull-postgres-bee20e200198) and quite a bit of practice using docker for my own projects. For example, if you have a basic CRUD api and frontend app for your project, you can run both the database and web server as docker containers. This is great for getting teammates all able to run a single configuration without messing with your environments. Or its good if you personally change your development machine often.

But I recommend doing what's most convenient for you. Getting the database as a container is nice, but I dont mind running the webserver on node using my own pc. But the more environment setup your project needs, considering taking the time to write the appropriate Dockerfile and docker-compose.yml if you need it.
