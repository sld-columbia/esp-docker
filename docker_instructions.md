# Docker

The ESP Docker images are available on Dockerhub:
[hub.docker.com/repository/docker/columbiasld/esp](https://hub.docker.com/repository/docker/columbiasld/esp).

The repository containing the Dockerfiles for generating the ESP
Docker images is available on Github:
[github.com/sld-columbia/esp-docker](https://github.com/sld-columbia/esp-docker).

The ESP Docker images based on a CentOS 7 Docker image contain:

* the installation of all the [software packets](https://esp.cs.columbia.edu/docs/setup/setup-guide/#software-packets)
  required by ESP;

* the installation of utilities like vim, emacs, tmux, socat and
  minicom, useful when working with ESP;

* an [environment variables](https://esp.cs.columbia.edu/docs/setup/setup-guide/#environment-variables) setup script to
  be customized with the correct CAD tools paths and licenses;

* the [ESP repository](https://esp.cs.columbia.edu/docs/setup/setup-guide/#esp-repository) and all its submodules;

* the installation of the [software toolchains](https://esp.cs.columbia.edu/docs/setup/setup-guide/#software-toolchain)
  for RISC-V and Leon3.

There are two types of images, which are identified by the `centos7-full` and
`centos7-small` strings in the tag. The complete images are labeled with
`centos7-full` and they are over 5GB in size in the compressed format. We also
offer smaller images labeled with the `small` string that are slightly
above 1GB in size, because they do not include the installation of the
RISC-V and Leon3 [software toolchains](https://esp.cs.columbia.edu/docs/setup/setup-guide/#software-toolchain).

We have tested the ESP Docker images on Windows 10, MacOS 10.15,
CentOS 7, Ubuntu 18.04 and RedHat 7.8. However, they should work on
other OS distributions as well.


## Install Docker

If you haven't already, install Docker by following the instructions
for your OS:
[docs.docker.com/engine/install](https://docs.docker.com/engine/install/).

To start the Docker daemon on Linux systems you need to run:

```bash
sudo systemctl start docker
```
<br>

On Linux systems you will need to use `sudo` to run all `docker`
commands (`sudo docker`) unless you add your user to the `docker`
group. To add your user to the `docker` group run the following:

```bash
sudo usermod -aG docker ${USER}
```
<br>

Then you need to logout and log back in and after that you can verify
you are part of the `docker` group by running `groups`.

[Back to top](#)

## Download the Docker image

Download the Docker image by running the following command (on Windows
10 you should run it in the PowerShell). Replace `<tag>` with
`centos7-full` for the full image and with `centos7-small` for the
small image.

```bash
docker pull columbiasld/esp:<tag>
```
<br>

You may need to add login credentials to be able to pull. In that case
you can add your credentials by running the following. A prompt will
ask for your password as well.

```bash
docker login -u <your-dockerhub-username>
```
<br>


## Start the Docker container

### Linux

On Linux you can start a Docker container in interactive mode as
follows. The `security-opt`, `network`, `env` and `volume` arguments
are needed to connect the container to the host machine display, so
that it's possible to open graphical interfaces from inside the
container. Replace `<tag>` with `centos7-full` for the full image and
with `centos7-small` for the small image.

```bash
docker run -it --user espuser --security-opt label=type:container_runtime_t --network=host -e DISPLAY=$DISPLAY -v "$HOME/.Xauthority:/root/.Xauthority:rw" -v "/opt:/opt" columbiasld/esp:<tag> /bin/bash
```
### MacOS

On MacOS you need to make sure that the X server is running and is
configured properly to be able to open graphical interfaces from
inside the Docker container:

* Install [XQuartz](https://www.xquartz.org/)

* Open a terminal and launch the application with `open -a xQuartz`.

* From the XQuartz drop-down menu, open `Preferences..` and select the `Security`
tab. Make sure that the check box "Allow connections from network clients" is
enabled, as shown in the picture below.

![](./images/xquartz_preferences.png "XQuartz Security Settings")

* From the same drop-down menu, quit XQuartz. The application must
exit completely, then you should restart XQuartz.

* From a new terminal window, enable X forwarding for your current IP address:
	```bash
	ip=$(ifconfig en0 | grep inet | awk '$1=="inet" {print $2}')
	xhost + $ip
	```
	<br>

Finally, you can run the container as follows. Replace `<tag>` with
`centos7-full` for the full image and with `centos7-small` for the
small image.

```bash
docker run -it --network=host -e DISPLAY=$ip:0 -v /tmp/.X11-unix:/tmp/.X11-unix columbiasld/esp:<tag> /bin/bash
```

### Windows 10

On Windows 10 you can start a Docker container in interactive mode
from the Docker dashboard GUI by clicking first on the _RUN_ button
next to the Docker image name and then on the _CLI_ button next to the
running container.

To be able to launch graphical interfaces from inside the container on
Windows 10 you need a couple of extra steps (adapted from these
guides:
[guide1](https://dev.to/darksmile92/run-gui-app-in-linux-docker-container-on-windows-host-4kde),
[guide2](https://medium.com/@potatowagon/how-to-use-gui-apps-in-linux-docker-container-from-windows-host-485d3e1c64a3)):

* Install [VcXsrv Windows X
  Server](https://sourceforge.net/projects/vcxsrv/) and after that
  launch _XLaunch_. In the _XLaunch_ configuration steps use all the
  default configurations apart from selecting the "Disable access
  control" option, which is not selected by default.

* Find the IP of you Windows machine by running `ipconfig` in the
  PowerShell. Look for the `IPv4 Address` entry.

* Run `export DISPLAY=<host-ip-address>:0.0` in the Docker container
  that you previously started with the _CLI_ button.

### Test X forwarding

From inside the container you can test the connection to the host display by
running `xeyes` or `xclock`.

[Back to top](#)

## CAD tools with Docker

The [CAD tools](https://esp.cs.columbia.edu/docs/setup/setup-guide/#cad-tools) 
section has the complete list of CAD tools
required by ESP. The Docker image doesn't contain any of those CAD
tools. Hence, next we describe a few options to work with CAD tools in
the Docker container.

### Install the CAD tools on your host machine from inside the container

Installing the CAD tools from inside the Docker container is
convenient because all required packages are already
installed. Moreover, this strategy is especially useful if your host
machine doesn't run CentOS 7, which is the OS running in the Docker
container. However, instead of installing the CAD tools inside the
container, it's preferable to install them on the host machine, so
that they can be used also by other containers or even natively on the
host machine in some case. In addition, it's better to avoid committing
to the Docker image the large CAD tools installation folders.

Installing the CAD tools on the host machine from inside the container
can be done by using
[volumes](https://docs.docker.com/storage/volumes/) or [bind
mounts](https://docs.docker.com/storage/bind-mounts/), which are the
two main ways for persisting data generated by and/or used by Docker
containers. Use bind mounts if you want to specify the absolute path
on the host machine where the CAD tools should be. Use volumes if you
want the data to live inside Docker's storage directory, which is
managed by Docker (this is preferable on Windows and MacOS).

You can declare multiple volumes and bind mounts when you launch the container
with `docker run` by adding the following arguments.

* Volume: `-v <volume-name>:/cad-tools-path/inside/container`. The
  volume called `<volume-name>` lives inside the Docker's storage
  directory and it will be accessible from inside the container at the
  path `/cad-tools-path/inside/container`.

* Bind mount: `-v
  /cad-tools-path/on/host:/cad-tools-path/inside/container`. The file
  or folder `/cad-tools-path/on/host` will be accessible from inside
  the container at the path `/cad-tools-path/inside/container`.

The idea is that from inside the container you can install the CAD
tools at the path(s) of the volumes or bind mounts that you
defined. Once the tools are installed, every container can access them
if it receives the proper volume or bind mount arguments.

### Use CAD tools already installed on your host machine

This option is useful if you already have some of the CAD tools
installed on your host machine and if the host machine OS matches the
one of the Docker container. In that case you can simply give access
to the CAD tools with a bind mount when you start a container, by
passing the `-v` argument as decribed in the previous section: `-v
/cad-tools-path/on/host:/cad-tools-path/inside/container`.

### Install the CAD tools inside the container

If you want a fully self-contained container, you can install the
tools directly inside the container, without defining any volumes or
bind mounts. The issue with this solution is that the container size
will increase considerably, making it less portable.


## Environment variables with Docker

The ESP Docker image provides two scripts for setting the required
environment variables, as specified in the [environment
variables](#environment-variables) section.

Source the `esp_env.sh` script if you don't need to use any CAD tools.
```bash
cd /home/espuser
source esp_env.sh
```
<br>
If you need some CAD tools you can use the `esp_env_cad.sh`
instead. You should customize the script by inserting the paths of
your tools and licences and by commenting out the environment variables for
the tools that you don't have. Then you should source the script.

```bash
cd /home/espuser
source esp_env_cad.sh
```
<br>
[Back to top](#)

## Useful Docker commands

Exit a container:
```bash
# run from inside the container
exit
```
<br>
List all local containers and their IDs:
```bash
docker ps -a
```
<br>
Stop a container:
```bash
docker stop <container-ID>
```
<br>
Start a container:
```bash
docker start <container-ID>
```
<br>
Attach to a running container:
```bash
docker attach <container-ID>
```
<br>
Copy data from host machine to a container:
```bash
docker cp <path-on-host> <container-ID>:/<path-inside-container>
```
<br>

Delete a container:
```bash
docker rm -f <container-ID>
```
<br>
List all local images:
```bash
docker images
```
<br>
Delete an image:
```bash
docker rmi <image-name>
```
<br>
Here is the complete [Docker documentation](https://docs.docker.com/).

[Back to top](#)

---
