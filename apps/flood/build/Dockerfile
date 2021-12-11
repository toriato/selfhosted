FROM alpine
ENV UNAME=flood
ENV UID=1000
ENV GID=1000
ENV VERSION=4.7.0

# install dependencies
RUN apk add --update --no-cache mediainfo

# download prebuilt file
RUN wget "https://github.com/jesec/flood/releases/download/v$VERSION/flood-linux-x64" -O /bin/flood ;\
  chmod +x /bin/flood

# create user
RUN adduser -D -h /data -u $UID $UNAME ;\
  chown $UID:$GID /data

USER $UNAME

CMD [ "flood", "-h", "0.0.0.0", "-d", "/data" ]
