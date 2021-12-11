FROM nginx:alpine AS builder
ENV HEADERS_MORE_VERSION  0.33

# setup configure arguments
RUN CONFARGS=$(nginx -V 2>&1 | sed -n -e 's/^.*arguments: //p')

# install dependencies
RUN apk add --no-cache --virtual .build-deps \
  build-base \
  pcre-dev \
  zlib-dev

# download and extract sources
RUN mkdir /src ;\
  curl -s "http://nginx.org/download/nginx-$NGINX_VERSION.tar.gz" | tar zxC /src ;\
  curl -s "https://codeload.github.com/openresty/headers-more-nginx-module/tar.gz/v$HEADERS_MORE_VERSION" | tar zxC /src

# build nginx dynamic modules
RUN cd /src/nginx-$NGINX_VERSION ;\
  ./configure --with-compat $CONFARGS \
  --add-dynamic-module=/src/headers-more-nginx-module-$HEADERS_MORE_VERSION ;\
  make modules -j8 ;\
  mkdir /dist ;\
  cp objs/*.so /dist ;\
  cd / ;\
  rm -rf /src

RUN apk del .build-deps

FROM nginx:alpine

# copy dynamic modules
COPY --from=builder \
  /dist/ngx_http_headers_more_filter_module.so \
  /usr/local/nginx/modules/ngx_http_headers_more_filter_module.so
