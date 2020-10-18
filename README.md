# Repository for creating Docker images for ESP

## Creating the Docker images

## CentOS 7

To create Docker image based on CentOS 7:
```bash
# for full image
cd centos7/full
# for small image
cd centos7/small

docker build -t <docker-image-name>
```

## Using the existing images on DockerHub

The images are available
[here](https://hub.docker.com/repository/docker/davidegiri/esp-tutorial-micro2020). The
instructions on how to use them are available
[here](https://www.esp.cs.columbia.edu/tutorials/micro2020/docker/)
