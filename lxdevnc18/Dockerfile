################################################################################
# base
################################################################################
FROM ubuntu:18.04 as system

# built-in packages
ENV DEBIAN_FRONTEND noninteractive
RUN apt update \
    && apt install -yq --no-install-recommends software-properties-common curl apache2-utils \
    && apt update \
    && apt install -yq --no-install-recommends --allow-unauthenticated \
        supervisor nginx sudo net-tools zenity xz-utils \
        dbus-x11 x11-utils screen git locales \
        mesa-utils libgl1-mesa-dri openssh-server \
        lsb-release net-tools apt-utils \
    && apt autoclean -y \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

RUN echo "install vnc"
RUN apt update \
    && apt install -yq --no-install-recommends --allow-unauthenticated \
        xvfb x11vnc libxi6 \
        vim nano wget gpg-agent \
    && apt autoclean -y \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

RUN echo "install gtk"
RUN apt update \
    && apt install -yq --no-install-recommends --allow-unauthenticated \
        lxde lxtask desktop-file-utils \
		gtk2-engines-murrine gnome-themes-standard gtk2-engines-pixbuf gtk2-engines-murrine arc-theme \
    && apt autoclean -y \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

RUN echo "install font"
# Language
RUN apt update \
    && apt install -yq --no-install-recommends --allow-unauthenticated \
        ttf-ubuntu-font-family ttf-wqy-zenhei fonts-ipafont \
	language-pack-zh-hans ttf-wqy-microhei xfonts-wqy \
	language-pack-gnome-zh-hans \
    && apt autoclean -y \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

RUN echo "install mysoftware"
RUN apt update \
    && apt install -yq --no-install-recommends --allow-unauthenticated \
        cron mpv w3m python-dev libffi-dev libssl-dev \
        rsync aria2 zip python-pip python3-pip ffmpeg psmisc rsyslog \
        p7zip-full p7zip-rar upx-ucl unrar iputils-ping id3v2 \
        unzip python-setuptools python3-setuptools \
        libcurl4-openssl-dev libgirepository1.0-dev \
        libglib2.0-dev build-essential module-init-tools \
    && apt autoclean -y \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

### Install nss-wrapper to be able to execute image as non-root user
RUN apt-get update && apt-get install -yq \
        gettext \
        libnss-wrapper \
    && apt autoclean -y \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

# A tiny but valid `init` for containers
ARG TINI_VERSION=v0.19.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /bin/tini
RUN chmod +x /bin/tini

################################################################################
# build x11vnc-git
################################################################################
FROM ubuntu:18.04 as x11vnc-git

WORKDIR /root
RUN apt-get update && apt-get install git -yq \
    && apt autoclean -y \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

#RUN cp /etc/apt/sources.list /etc/apt/sources.list~
RUN sed -Ei 's/^# deb-src /deb-src /' /etc/apt/sources.list
RUN apt-get update && apt-get build-dep x11vnc -yq

RUN cd /root && git clone https://github.com/LibVNC/x11vnc \
	&& cd /root/x11vnc/ \
	&& autoreconf -fiv \
	&& ./configure \
	&& make \
	&& make install \
	&& apt autoclean -y \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

################################################################################
# image
################################################################################
FROM system

COPY --from=x11vnc-git  /usr/local/bin/x11vnc  /usr/bin/x11vnc
COPY src /
RUN chmod +x /usr/bin/x11vnc
RUN chmod +x /startup.sh
RUN chmod +x /usr/local/bin/xvfb.sh

EXPOSE 80
WORKDIR /root

ARG ARG_VNC_RESOLUTION
ARG ARG_VNC_PW
ARG ARG_USER
ARG ARG_PASSWORD
ARG ARG_VNC_PORT

ENV \
    USER=${ARG_USER:-ubuntu} \
    PASSWORD=${ARG_PASSWORD:-ubuntu} \
    VNC_PW=${ARG_VNC_PW:-ubuntu} \
    VNC_RESOLUTION=${ARG_VNC_RESOLUTION:-1024x768} \
	VNC_PORT=${ARG_VNC_PORT:-5900} \
    SSH_KEY=**None** \
    SHELL=/bin/bash
ENTRYPOINT ["/startup.sh"]
