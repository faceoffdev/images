ARG PHP_CLI_IMAGE
ARG PHP_VERSION=${PHP_VERSION:-"8.4"}
ARG PHP_PREFIX=${PHP_PREFIX:-"84"}
FROM ${PHP_CLI_IMAGE}:${PHP_VERSION} AS php_cron

ARG PHP_VERSION
ARG PHP_PREFIX

USER root

RUN apk add --no-cache --update --repository=https://dl-cdn.alpinelinux.org/alpine/edge/community \
    supercronic \
    php${PHP_PREFIX}-posix

RUN rm -rf /tmp/* \
      /var/{cache,log}/* \
      /etc/crontabs/*

COPY --link rootfs /

RUN chown -R ${USER}:${USER} /etc/crontabs \
    && chown -R ${USER}:${USER} /etc/supervisor

USER ${USER}


FROM php_cron AS php_cron_dev

ENV PHP_OPCACHE_VALIDATE_TIMESTAMPS=1
