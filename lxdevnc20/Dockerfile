################################################################################
# base system
################################################################################

FROM ubuntu:20.04 as system

# built-in packages
ENV DEBIAN_FRONTEND noninteractive
RUN apt update \
    && apt install -yq --no-install-recommends software-properties-common curl apache2-utils \
    && apt update \
    && apt install -yq --no-install-recommends --allow-unauthenticated \
        supervisor nginx sudo net-tools zenity xz-utils \
        dbus-x11 x11-utils screen git \
        mesa-utils libgl1-mesa-dri openssh-server apt-utils \
    && apt autoclean -y \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

RUN echo "install vnc"
RUN apt update \
    && apt install -yq --no-install-recommends --allow-unauthenticated \
        xvfb x11vnc \
		vim nano wget gpg-agent \
    && apt autoclean -y \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

RUN echo "install gtk"
RUN apt update \
    && apt install -yq --no-install-recommends --allow-unauthenticated \
        lxde gtk2-engines-murrine gnome-themes-standard gtk2-engines-pixbuf gtk2-engines-murrine arc-theme \
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
        cron mpv w3m python-dev-is-python2 libffi-dev libssl-dev \
        rsync aria2 zip python3-pip ffmpeg psmisc rsyslog \
        p7zip-full p7zip-rar upx-ucl unrar iputils-ping id3v2 \
        python-setuptools python3-setuptools \
        libcurl4-openssl-dev libgirepository1.0-dev \
        libglib2.0-dev build-essential kmod \
		lxterminal \
    && apt autoclean -y \
    && apt autoremove -y \
    && rm -rf /var/lib/apt/lists/*

# A tiny but valid `init` for containers
ARG TINI_VERSION=v0.19.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /bin/tini
RUN chmod +x /bin/tini

RUN cp -R /usr/share/icons/ubuntu-mono-light/* /usr/share/icons/hicolor/

################################################################################
# image
################################################################################
FROM system

COPY src /
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
