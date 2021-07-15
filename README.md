# ESP with Docker

## ESP Docker images

The pre-built images are available [here](https://hub.docker.com/repository/docker/columbiasld/esp).

## How to use the ESP Docker image

See the [instructions on the ESP website](https://www.esp.cs.columbia.edu/docs/setup/setup-guide/#docker).

## How to create the ESP Docker images

To create a Docker image based on different configurations: 
```
docker build -t <docker-image-name> -f <docker-file-name> .
```

Example:

```bash
docker build -t columbiasld/esp:centos7-full -f Dockerfile.centos7_full .
docker build -t columbiasld/esp:centos7-small -f Dockerfile.centos7_small .
docker build -t columbiasld/esp:ubuntu18-full -f Dockerfile.ubuntu18_full .
docker build -t columbiasld/esp:ubuntu18-small -f Dockerfile.ubuntu18_small .
```
