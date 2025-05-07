ARG ALPINE_VERSION=${ALPINE_VERSION:-"latest"}
FROM alpine:${ALPINE_VERSION} AS php_cli

ARG PHP_VERSION=${PHP_VERSION:-"8.4"}
ARG PHP_PREFIX=${PHP_PREFIX:-"84"}

ENV PHP_POST_MAX_SIZE=${PHP_POST_MAX_SIZE:-"8M"} \
    PHP_UPLOAD_MAX_FILESIZE=${PHP_UPLOAD_MAX_FILESIZE:-"2M"} \
    PHP_MAX_EXECUTION_TIME=${PHP_MAX_EXECUTION_TIME:-"30"} \
    PHP_MAX_INPUT_TIME=${PHP_MAX_INPUT_TIME:-"60"} \
    PHP_DEFAULT_SOCKET_TIMEOUT=${PHP_DEFAULT_SOCKET_TIMEOUT:-"120"} \
    PHP_MEMORY_LIMIT=${PHP_MEMORY_LIMIT:-"256M"} \
    PHP_OPCACHE_MAX_ACCELERATED_FILES=${PHP_OPCACHE_MAX_ACCELERATED_FILES:-16229} \
    PHP_OPCACHE_ENABLE_CLI=${PHP_OPCACHE_ENABLE_CLI:-0} \
    PHP_OPCACHE_MEMORY_CONSUMPTION=${PHP_OPCACHE_MEMORY_CONSUMPTION:-256} \
    PHP_OPCACHE_VALIDATE_TIMESTAMPS=${PHP_OPCACHE_VALIDATE_TIMESTAMPS:-0} \
    PHP_OPCACHE_INTERNED_STRINGS_BUFFER=${PHP_OPCACHE_INTERNED_STRINGS_BUFFER:-64} \
    PHP_OPCACHE_JIT_BUFFER_SIZE=${PHP_OPCACHE_JIT_BUFFER_SIZE:-"512M"}

ENV TZ=Europe/Moscow

RUN apk add --no-cache --update --repository=https://dl-cdn.alpinelinux.org/alpine/edge/community \
    tzdata \
    tini \
    supervisor \
    curl \
    nano \
    git \
    bash

RUN mkdir -p /etc/php && ln -sf /etc/php /etc/php${PHP_PREFIX}

RUN apk add --no-cache --update --repository=https://dl-cdn.alpinelinux.org/alpine/edge/community \
    php${PHP_PREFIX} \
    php${PHP_PREFIX}-phar \
    php${PHP_PREFIX}-openssl \
    php${PHP_PREFIX}-ctype \
    php${PHP_PREFIX}-bcmath \
    php${PHP_PREFIX}-mbstring \
    php${PHP_PREFIX}-json \
    php${PHP_PREFIX}-opcache \
    php${PHP_PREFIX}-fileinfo \
    php${PHP_PREFIX}-curl \
    php${PHP_PREFIX}-redis \
    php${PHP_PREFIX}-tokenizer \
    php${PHP_PREFIX}-gd \
    php${PHP_PREFIX}-dom \
    php${PHP_PREFIX}-simplexml \
    php${PHP_PREFIX}-xml \
    php${PHP_PREFIX}-xmlwriter \
    php${PHP_PREFIX}-xmlreader \
    php${PHP_PREFIX}-iconv \
    php${PHP_PREFIX}-zip \
    php${PHP_PREFIX}-pdo_pgsql \
    php${PHP_PREFIX}-pdo_mysql \
    php${PHP_PREFIX}-pcntl

RUN ln -sf /usr/bin/php${PHP_PREFIX} /usr/bin/php

RUN rm -rf /tmp/* \
    /var/{cache,log}/*

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
COPY --link rootfs /

ARG UID=1000
ARG GID=1000
ENV USER='application' \
    WORKDIR='/app'

RUN addgroup --gid ${GID} --system ${USER} \
    && adduser --uid ${UID} --system --ingroup ${USER} ${USER} \
    && mkdir -p ${WORKDIR} \
    && chown -R ${USER}:${USER} \
      ${WORKDIR}  \
      /home/${USER} \
      /run

USER ${USER}

WORKDIR ${WORKDIR}

RUN bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)" && \
sed -i 's/OSH_THEME="font"/OSH_THEME="agnoster"/' ~/.bashrc

ENTRYPOINT ["/sbin/tini", "--", "/usr/bin/docker-entrypoint.sh"]

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"]


FROM php_cli AS php_cli_dev

ENV XDEBUG_CLIENT_HOST=${XDEBUG_CLIENT_HOST:-172.17.0.1} \
    XDEBUG_PORT=${XDEBUG_PORT:-9003} \
    XDEBUG_IDEKEY=${XDEBUG_IDEKEY:-PHPSTORM} \
    XDEBUG_MODE=${XDEBUG_MODE:-debug}

USER root

RUN apk add --no-cache --update --repository=https://dl-cdn.alpinelinux.org/alpine/edge/community \
    php${PHP_PREFIX}-pecl-xdebug

# Мы выдаем возможность application использовать команду sudo
RUN apk add --no-cache --update sudo
RUN addgroup ${USER} wheel && \
    echo "%wheel ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

USER ${USER}
