################
# STAGE 1
################
FROM golang:1.17-alpine as server
WORKDIR /src

# install dependencies
RUN apk add --no-cache --virtual .build-deps \
  build-base git cmake libx11-dev libxrandr-dev libxtst-dev \
  gstreamer-dev gst-plugins-base-dev gst-plugins-good gst-plugins-bad gst-plugins-ugly

RUN git clone https://github.com/jtanx/libclipboard ;\
  cd libclipboard ;\
  cmake . ;\
  make -j4 ;\
  make install ;\
  cd /src ;\
  rm -rf libclipboard

# build neko server
RUN git clone https://github.com/m1k1o/neko ;\
  cd neko/server ;\
  go get -v -t -d . ;\
  go build -o /usr/local/bin/neko cmd/neko/main.go ;\
  cd /src ;\
  rm -rf neko

# clean up
RUN apk del .build-deps

################
# STAGE 2
################
FROM node:14-alpine as client
WORKDIR /src

# install dependencies
RUN apk add --no-cache --virtual .build-deps git
RUN git clone https://github.com/m1k1o/neko ;\
  cd neko/client ;\
  npm install ;\
  npm run build

# clean up
RUN apk del .build-deps

################
# STAGE 3
################
FROM alpine
ARG UNAME=neko
ARG UID=1000
ARG GID=1000

# install dependencies
RUN apk add --no-cache curl ca-certificates supervisor \
  xorg-server dbus-x11 xf86-video-dummy pulseaudio \
  cairo libxcb libxrandr libxv opus libvpx \
  gstreamer-dev gst-plugins-base-dev gst-plugins-good gst-plugins-bad gst-plugins-ugly\
  font-noto font-noto-emoji font-noto-cjk font-noto-cjk-extra \
  bash

# create runtime user
RUN cat /etc/passwd
RUN adduser -D -s $(which bash) -u $UID $UNAME ;\
  addgroup $UNAME video ;\
  addgroup $UNAME audio ;\
  addgroup $UNAME pulse

# setup pulseaudio
RUN mkdir -p /home/$UNAME/.config/pulse ;\
  echo "default-server=unix:/tmp/pulseaudio.socket" > /home/$UNAME/.config/pulse/client.conf

# setup directories for neko
RUN mkdir -p /var/log/neko /var/www; \
  # chmod 1777 /var/log/neko; \
  chown $UNAME /var/log/neko; \
  chown -R $UNAME /home/$UNAME

# workaround for an X11 problem: http://blog.tigerteufel.de/?p=476
RUN mkdir /tmp/.X11-unix; \
  # chmod 1777 /tmp/.X11-unix; \
  chown $UNAME /tmp/.X11-unix/

# set default envs
ENV USER=$UNAME
ENV DISPLAY=:99.0
ENV NEKO_PASSWORD=neko
ENV NEKO_PASSWORD_ADMIN=admin
ENV NEKO_BIND=:8080

HEALTHCHECK --interval=10s --timeout=5s --retries=8 \
  CMD wget -O - http://localhost:${NEKO_BIND}/health || exit 1

# copy config files
COPY --from=client /src/neko/.docker/base/dbus /usr/bin/dbus
COPY --from=client /src/neko/.docker/base/default.pa /etc/pulse/default.pa
COPY rootfs/etc/ /etc/

# copy static files from previous stages
COPY --from=server /usr/local/bin/neko /usr/bin/neko
COPY --from=client /src/neko/client/dist /var/www

# run neko
CMD /usr/bin/supervisord
