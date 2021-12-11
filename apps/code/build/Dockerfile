FROM alpine
ENV UID                  1000
ENV GID                  1000
ENV UNAME                 code
ENV GROUP                 code
ENV VERSION               3.12.0

RUN apk add --no-cache \
  dumb-init \
  libstdc++ \
  libc6-compat \
  bash \
  sudo \
  curl \
  nodejs

RUN mkdir /usr/lib/code-server ;\
  curl -sSL "https://github.com/cdr/code-server/releases/download/v$CODE_SERVER_VERSION/code-server-$CODE_SERVER_VERSION-linux-amd64.tar.gz" | tar -xzC /usr/lib/code-server --strip=1

RUN adduser -DG -s /bin/bash -u $UID $UNAME ;\
  echo "$UNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/$UNAME

USER $UNAME
WORKDIR /home/$UNAME
RUN mkdir -p .config/code-server .local/share/code-server

ENTRYPOINT [ "dumb-init", "--" ]
CMD [ "node", "/usr/lib/code-server/out/node/entry.js" ]
