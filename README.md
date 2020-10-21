# ESP with Docker

## ESP Docker images

The images are available [here](https://hub.docker.com/repository/docker/davidegiri/esp).

## How to use the ESP Docker image

See the [instructions on the ESP website](https://www.esp.cs.columbia.edu/docs/setup/setup-guide/#docker).

## How to creating the ESP Docker images

### CentOS 7

To create Docker image based on CentOS 7:
```bash
# for full image
cd centos7/full
# for small image
cd centos7/small

docker build -t <docker-image-name>
```
