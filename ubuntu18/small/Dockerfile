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
RUN git clone https://github.com/sld-columbia/esp.git && cd esp && \
        git submodule update --init --recursive \
        accelerators/stratus_hls/common/inc \
        accelerators/chisel/hw rtl/caches/esp-caches rtl/cores/ariane/ariane \
        soft/ariane/riscv-pk soft/ariane/riscv-tests \
        accelerators/third-party/NV_NVDLA/ip \
        accelerators/third-party/NV_NVDLA/sw && \
        cd /home/espuser/esp/accelerators/third-party/NV_NVDLA && \
        rm -rf ip/verif sw/prebuilt sw/regression

# copy over bashrc script
COPY bashrc /home/espuser/.bashrc

# copy over useful config files for tmux, emacs, vim
COPY tmux.conf /home/espuser/.tmux.conf
COPY emacs /home/espuser/.emacs
COPY vimrc /home/espuser/.vimrc

ENV PATH="/opt/riscv/bin:$PATH"
ENV RISCV="/opt/riscv"

USER root

# enable X11 forwarding
RUN sed -i 's/#   ForwardX11 no/ForwardX11 yes/g' /etc/ssh/ssh_config

# copy over environment setup script (without CAD tools)
COPY esp_env.sh /home/espuser

# copy over environment setup script (with CAD tools)
COPY esp_env_cad.sh /home/espuser

# copy over bashrc script
COPY bashrc /root/.bashrc

# copy over useful config files for tmux, emacs, vim
COPY tmux.conf /root/.tmux.conf
COPY emacs /root/.emacs
COPY vimrc /root/.vimrc
