FROM ubuntu:20.04
ENV DEBIAN_FRONTEND=noninteractive

# need to install vulkan sdk before xrdp install
# install the vulkan sdk using the lunarg packages + etc
RUN apt-get update \
    && apt-get install -y --no-install-recommends git gnupg2 wget ca-certificates \
    && apt-get install -y make gcc g++ mesa-common-dev libglu1-mesa-dev libxxf86dga-dev libxxf86vm-dev libasound2-dev libx11-dev libxcb1-dev \
    && wget -qO - https://packages.lunarg.com/lunarg-signing-key-pub.asc | apt-key add - \
    && wget -qO /etc/apt/sources.list.d/lunarg-vulkan-focal.list https://packages.lunarg.com/vulkan/lunarg-vulkan-focal.list \
    && apt update \
    && apt install -y mesa-utils vulkan-sdk libxext-dev

RUN apt-get update \
    && DEBIAN_FRONTEND="noninteractive" apt-get install -y --no-install-recommends \
        dbus-x11 \
        git \
        locales \
        pavucontrol \
        pulseaudio \
        pulseaudio-utils \
        sudo \
        x11-xserver-utils \
        xfce4 \
        xfce4-goodies \
        xfce4-pulseaudio-plugin \
        xorgxrdp \
        xrdp \
        xubuntu-icon-theme \
    && rm -rf /var/lib/apt/lists/*
	
RUN echo "root:root" | chpasswd

ENV LANG en_US.UTF-8

EXPOSE 3389/tcp
ENTRYPOINT /etc/init.d/xrdp start &&tail -F /va/log/xrdp-sesman.log
