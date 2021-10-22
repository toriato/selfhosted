FROM nginx:alpine AS builder

ENV FANCYINDEX_VERSION    0.5.1
ENV HEADERS_MORE_VERSION  0.33

RUN CONFARGS=$(nginx -V 2>&1 | sed -n -e 's/^.*arguments: //p')

# 빌드에 필요한 라이브러리 및 도구 받기
RUN apk add --no-cache --virtual .build-deps \
  gcc \
  libc-dev \
  make \
  openssl-dev \
  pcre-dev \
  zlib-dev \
  linux-headers \
  curl \
  gnupg \
  libxslt-dev \
  gd-dev \
  geoip-dev

# 소스 받고 압축 풀기
RUN mkdir /usr/src
RUN curl -s "http://nginx.org/download/nginx-$NGINX_VERSION.tar.gz" | tar zxC /usr/src
RUN curl -s "https://codeload.github.com/aperezdc/ngx-fancyindex/tar.gz/v$FANCYINDEX_VERSION" | tar zxC /usr/src
RUN curl -s "https://codeload.github.com/openresty/headers-more-nginx-module/tar.gz/v$HEADERS_MORE_VERSION" | tar zxC /usr/src

# 빌드
RUN cd /usr/src/nginx-$NGINX_VERSION && \
  ./configure --with-compat $CONFARGS \
  --add-dynamic-module=/usr/src/ngx-fancyindex-$FANCYINDEX_VERSION \
  --add-dynamic-module=/usr/src/headers-more-nginx-module-$HEADERS_MORE_VERSION && \
  make && make install

FROM nginx:alpine
STOPSIGNAL SIGTERM
CMD ["nginx", "-g", "daemon off;"]

# 빌드한 모듈 복사하기
COPY --from=builder \
  /usr/local/nginx/modules/ngx_http_fancyindex_module.so \
  /usr/local/nginx/modules/ngx_http_fancyindex_module.so
COPY --from=builder \
  /usr/local/nginx/modules/ngx_http_headers_more_filter_module.so \
  /usr/local/nginx/modules/ngx_http_headers_more_filter_module.so
