# Perl docker setup

**This comes from the perl template repository and should be updated when used in a project**

## Local setup

If one is not using itk-devs wrapper script. One simple needs the following steps to run it without.

```shell
docker network create frontend
docker compose up -d
```

To access the application, use the command below which will return host and port number.

```shell
docker compose port nginx 8080
```

## Extend the default docker image

To install new perl libraries directly in the docker image. Create `docker-compose.override.yml` and
`docker-compose.server.override.yml` (with the same content). Add the following to the files, thereby switching over to
building a new container image for the project.

The `docker-compose.override.yml` will automatically be used by docker compose in local development. The server override
needs to be added to the `.env.docker.local` file one the hosting server to include the override.

```yaml
services:
  perl:
    build:
      dockerfile: Dockerfile
      context: ./docker/app
```

Create the new Docker image definition file.

```shell
mkdir .docker/app
touch .docker/app/Dockerfile
```

Extend from the main perl image an add the required libraries or other packages required for the project.

```Dockerfile
FROM itkdev/perl

RUN DEBIAN_FRONTEND=noninteractive \
    apt-get update \
    apt-get install -y \
    libcrypt-eksblowfish-perl \
    libcrypt-urandom-perl \
&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
```

### Running with the extended image.

This will ensure that the image is build everytime you run `up`, it should also be picked up when running the command
without `build`. This just makes it explicit.

```shell
docker composer up --build -d
```

You can also run `docker compose build --no-cache` to force rebuild the image.
