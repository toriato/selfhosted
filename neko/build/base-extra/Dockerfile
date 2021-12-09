FROM aeon/neko:base
ARG UNAME=neko
ARG UID=1000
ARG GID=1000

# install essential packages
RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories
RUN apk add --no-cache i3wm-gaps ibus ibus-hangul

# copy configurations
COPY rootfs/etc/ /etc/
COPY --chown=$UNAME rootfs/home/neko/ /home/$UNAME/
