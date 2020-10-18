FROM centos:7

WORKDIR /home

# install python, perl,  CAD tools and software toolchain dependencies, miscellaneous
RUN yum install -y python python-pip python3 python3-pip python3-tkinter \
perl perl-YAML perl-XML-Simple perl-ExtUtils-MakeMaker \
perl-Thread-Queue perl-Capture-Tiny \
xterm csh ksh zsh tcl glibc-devel glibc-devel.i686 \
glibc-static glibc-static.i686 mesa-libGL.i686 mesa-libGLU.i686 mesa-libGL \
mesa-libGLU mesa-dri-drivers mesa-dri-drivers.i686 readline-devel \
readline-devel.i686 libXp libXp.i686 openmotif ncurses gdbm-devel \
gdbm-devel.i686 libSM libSM.i686 libXcursor libXcursor.i686 libXft \
libXft.i686 libXrandr libXrandr.i686 libXScrnSaver libXScrnSaver.i686 \
libmpc-devel libmpc-devel.i686 nspr nspr.i686 nspr-devel nspr-devel.i686 \
tk-devel dtc bison flex \
git wget bzip2 which gcc gcc-c++ unzip file patch bc \
texinfo texinfo-tex help2man emacs vim xorg-x11-apps java
RUN ln -s /lib64/libtiff.so.5 /lib64/libtiff.so.3 && \
ln -s /usr/lib64/libmpc.so.3 /usr/lib64/libmpc.so.2

# install Pmw module
RUN pip3 install Pmw

# create non-root user
RUN useradd -ms /bin/bash espuser
# switch to non-root user
USER espuser
WORKDIR /home/espuser

# clone ESP repository and install software toolchains
RUN git clone --recursive https://github.com/sld-columbia/esp.git && \
cd /home/espuser/esp/third-party/accelerators/dma64/NV_NVDLA && rm -rf ip/verif sw/prebuilt sw/regression && \
cd /home/espuser/esp && \
(echo y; echo /home/espuser/leon; echo 2; echo n; echo n; echo n; echo n) | bash utils/scripts/build_leon3_toolchain.sh && \
(echo y; echo /home/espuser/riscv; echo 2; echo n; echo n; echo n) | bash utils/scripts/build_riscv_toolchain.sh && \
rm -rf /home/espuser/esp/_riscv_build/riscv-gnu-toolchain && \
rm -rf /home/espuser/esp/_leon3_build/*tar*

USER root

# enable X11 forwarding
RUN sed -i 's/#   ForwardX11 no/ForwardX11 yes/g' /etc/ssh/ssh_config

# copy over environment setup script
COPY esp_env.sh /home/espuser
