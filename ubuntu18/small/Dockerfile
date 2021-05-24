FROM ubuntu:18.04

WORKDIR /home

 
RUN apt-get update -y
RUN apt-get install -y tzdata
RUN apt-get install -y build-essential


# install python, perl, CAD tools and software toolchain dependencies, miscellaneous
RUN apt-get install -y python python-pip python3 python3-pip python3-tk \ 
perl libyaml-perl libxml-perl \
xterm csh ksh zsh tcl \
build-essential libgl1-mesa-dev libglu1-mesa libgl1-mesa-dri \
libreadline-dev libxpm-dev libmotif-dev libncurses5 libncurses-dev \
libgdbm-dev libsm-dev libxcursor-dev libxft-dev libxrandr-dev \
libxss-dev libmpc-dev libnspr4 libnspr4-dev tk tk-dev flex rename \
zlib1g gcc-multilib device-tree-compiler bison xvfb \
gawk socat minicom tmux sudo cpio rsync net-tools \
git wget bzip2 unzip file patch bc x11-apps \
texinfo help2man emacs vim
 
# For older GUIs (e.g. Stratus)
RUN echo 'deb http://security.ubuntu.com/ubuntu xenial-security main' | tee -a /etc/apt/sources.list
RUN apt-get install -y libpng-dev

# QT
RUN apt-get install -y qtcreator

# install Pmw module
RUN pip3 install Pmw

# create a non-root user with sudo privilege (password:espuser)
RUN useradd -ms /bin/bash espuser
RUN usermod -aG sudo espuser

SHELL ["/bin/bash", "-o", "pipefail", "-c"]
RUN echo 'espuser:espuser' | chpasswd

# switch to non-root user
USER espuser
WORKDIR /home/espuser
 
# clone ESP repository and install software toolchains
RUN git clone --recursive https://github.com/sld-columbia/esp.git
##  && \
## cd /home/espuser/esp/accelerators/third-party/NV_NVDLA && rm -rf ip/verif sw/prebuilt sw/regression
## RUN cd /home/espuser/esp && rm -rf utils/zynq
## RUN cd /home/espuser/esp && \
## #(echo y; echo /home/espuser/leon; echo 2; echo n; echo n; echo n; echo n) | bash utils/toolchain/build_leon3_toolchain.sh && rm -rf /tmp/_leon3_build && \
## (echo y; echo /home/espuser/riscv; echo 2; echo n; echo n; echo n) | bash utils/toolchain/build_riscv_toolchain.sh && rm -rf /tmp/_riscv_build 
## #(echo y; echo /home/espuser/riscv32imc; echo 2; echo n) | bash utils/toolchain/build_riscv32imc_toolchain.sh && rm -rf /tmp/_riscv32imc_build

# copy over bashrc script
COPY bashrc /home/espuser/.bashrc

# copy over tmux.conf script
COPY tmux.conf /home/espuser/.tmux.conf

# copy over emacs script
COPY emacs /home/espuser/.emacs



USER root

# enable X11 forwarding
RUN sed -i 's/#   ForwardX11 no/ForwardX11 yes/g' /etc/ssh/ssh_config

# copy over environment setup script (without CAD tools)
COPY esp_env.sh /home/espuser

# copy over environment setup script (with CAD tools)
COPY esp_env_cad.sh /home/espuser

# copy over bashrc script
COPY bashrc /root/.bashrc

# copy over tmux.conf script
COPY tmux.conf /root/.tmux.conf

# copy over emacs script
COPY emacs /root/.emacs
