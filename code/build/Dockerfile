FROM alpine

ENV EUID                  1000
ENV EGID                  1000
ENV USER                  code
ENV GROUP                 code
ENV CODE_SERVER_VERSION   3.12.0

RUN apk add --update --no-cache \
  dumb-init \
  libstdc++ \
  libc6-compat \
  bash \
  sudo \
  curl \
  nodejs

RUN mkdir /usr/lib/code-server \
  && curl -sSL "https://github.com/cdr/code-server/releases/download/v$CODE_SERVER_VERSION/code-server-$CODE_SERVER_VERSION-linux-amd64.tar.gz" | tar -xzC /usr/lib/code-server --strip=1

RUN addgroup -Sg $EGID $GROUP \
  && adduser -DSG $GROUP -s /bin/bash -u $EUID $USER \
  && echo "$USER ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/$USER

USER $USER
WORKDIR /home/$USER
RUN mkdir -p .config/code-server .local/share/code-server
ENTRYPOINT [ "dumb-init", "--" ]
CMD [ "node", "/usr/lib/code-server/out/node/entry.js" ]
