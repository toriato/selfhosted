FROM php:8.0-fpm-alpine

ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

RUN chmod +x /usr/local/bin/install-php-extensions && \
  install-php-extensions apcu bcmath bz2 pdo_mysql exif smbclient intl imap gmagick gmp gd zip
