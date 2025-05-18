ARG PHP_VERSION=${PHP_VERSION:-"8.4"}
ARG PHP_PREFIX=${PHP_PREFIX:-"84"}
FROM ghcr.io/faceoffdev/images/php-cli:${PHP_VERSION} AS php_fpm

ARG PHP_VERSION
ARG PHP_PREFIX

ENV PHP_FPM_TYPE=${PHP_FPM_TYPE} \
    PHP_FPM_MAX_CHILDREN=${PHP_FPM_MAX_CHILDREN} \
    PHP_FPM_START_SERVERS=${PHP_FPM_START_SERVERS} \
    PHP_FPM_MAX_SPARE_SERVERS=${PHP_FPM_MAX_SPARE_SERVERS} \
    PHP_FPM_MIN_SPARE_SERVERS=${PHP_FPM_MIN_SPARE_SERVERS} \
    PHP_FPM_REQUEST_TERMINATE_TIMEOUT=${PHP_FPM_REQUEST_TERMINATE_TIMEOUT:-60} \
    PHP_FPM_MAX_REQUESTS=${PHP_FPM_MAX_REQUESTS} \
    PHP_FPM_PROCESS_IDLE_TIMEOUT=${PHP_FPM_PROCESS_IDLE_TIMEOUT:-"10s"}

USER root

RUN apk add --no-cache --update --repository=https://dl-cdn.alpinelinux.org/alpine/edge/community \
    nginx \
    php${PHP_PREFIX}-fpm \
    && ln -sf /usr/sbin/php-fpm${PHP_PREFIX} /usr/sbin/php-fpm

COPY --link rootfs /

RUN chown -R ${USER}:${USER} /var/log \
  && chown -R ${USER}:${USER} /var/lib \
  && chown -R ${USER}:${USER} /usr/lib \
  && chown -R ${USER}:${USER} /etc/php/conf.d

USER ${USER}

COPY --chown=${USER}:${USER} app ${WORKDIR}

EXPOSE 8080

HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:8080/fpm-ping


FROM php_fpm AS php_fpm_dev

ENV PHP_FPM_TYPE="ondemand" \
    PHP_FPM_MAX_CHILDREN=10 \
    PHP_FPM_MAX_REQUESTS=0 \
    PHP_FPM_PROCESS_IDLE_TIMEOUT="10s" \
    PHP_FPM_REQUEST_TERMINATE_TIMEOUT=300 \
    PHP_OPCACHE_VALIDATE_TIMESTAMPS=1
