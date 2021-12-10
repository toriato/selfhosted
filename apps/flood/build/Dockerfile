FROM alpine

RUN apk add --update --no-cache su-exec npm mediainfo
RUN npm i -g @jesec/flood
RUN mkdir /data

COPY ./rootfs /

ENTRYPOINT [ "/entrypoint" ]
CMD [ "flood", "-h", "0.0.0.0", "-p", "8000", "-d", "/data", "--auth", "none", "--rtsocket", "/run/rtorrent/rtorrent.sock" ]
