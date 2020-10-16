# Repository for creating Docker images for ESP

## CentOS 7

To create Docker image with CentOS 7:
```bash
cd centos7
docker build -t <docker-image-name>
```

To run and attach to image in interactive mode and with X:
```bash
docker run --tty --interactive --network=host --env DISPLAY=$DISPLAY --volume $XAUTH:/root/.Xauthority davidegiri/esp-tutorial-micro2020
```
