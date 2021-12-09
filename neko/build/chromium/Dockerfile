FROM aeon/neko:base-extra
ARG UNAME=neko
ARG UID=1000
ARG GID=1000

RUN apk add --no-cache chromium alsa-plugins-pulse

# copy configurations
COPY rootfs/etc/ /etc/
COPY --chown=$UNAME rootfs/home/neko/ /home/$UNAME/
