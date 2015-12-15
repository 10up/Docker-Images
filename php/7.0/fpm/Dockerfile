# Dockerfile written by 10up <sales@10up.com>
#
# Work derived from official PHP Docker Library:
# Copyright (c) 2014-2015 Docker, Inc.

FROM debian:jessie

ENV PHP_INI_DIR /usr/local/etc/php
RUN mkdir -p $PHP_INI_DIR/conf.d

ENV PHP_EXTRA_CONFIGURE_ARGS --enable-fpm --with-fpm-user=www-data --with-fpm-group=www-data

ENV GPG_KEYS 1A4E8B7277C42E53DBA9C7B9BCAA30EA9C0D5763
RUN set -xe \
	&& for key in $GPG_KEYS; do \
		gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
	done

ENV PHP_VERSION 7.0.0
ENV PHP_FILENAME php-7.0.0.tar.xz
ENV PHP_SHA256 7dbdda74c502960febe0544b3e3a7c430389a7a4260e94c73fd8ca26c33b8540

# --enable-mysqlnd is included below because it's harder to compile after the fact the extensions are (since it's a plugin for several extensions, not an extension in itself)
RUN buildDeps=" \
		bzip2 \
		xz-utils \
		file \
		libcurl4-openssl-dev \
		libreadline6-dev \
		libssl-dev \
		libxml2-dev \
		ca-certificates \
        curl \
        libxml2 \
        gcc \
        libc-dev \
        make \
        pkg-config \
	" \
	&& set -x \
	&& apt-get update && apt-get install -y $buildDeps --no-install-recommends && rm -rf /var/lib/apt/lists/* \
	&& curl -fSL "http://php.net/get/$PHP_FILENAME/from/this/mirror" -o "$PHP_FILENAME" \
	&& echo "$PHP_SHA256 *$PHP_FILENAME" | sha256sum -c - \
	&& curl -fSL "http://php.net/get/$PHP_FILENAME.asc/from/this/mirror" -o "$PHP_FILENAME.asc" \
	&& gpg --verify "$PHP_FILENAME.asc" \
	&& mkdir -p /usr/src/php \
	&& tar -xf "$PHP_FILENAME" -C /usr/src/php --strip-components=1 \
	&& rm "$PHP_FILENAME"* \
	&& cd /usr/src/php \
	&& ./configure \
		--with-config-file-path="$PHP_INI_DIR" \
		--with-config-file-scan-dir="$PHP_INI_DIR/conf.d" \
		$PHP_EXTRA_CONFIGURE_ARGS \
		--disable-cgi \
		--enable-mysqlnd \
		--with-curl \
		--with-openssl \
		--with-readline \
		--with-zlib \
	&& make -j"$(nproc)" \
	&& make install \
	&& { find /usr/local/bin /usr/local/sbin -type f -executable -exec strip --strip-all '{}' + || true; } \
	&& make clean
	&& apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false $buildDeps \
	&& apt-get autoremove

COPY docker-php-ext-* /usr/local/bin/

ENV extensionDeps=" \
        autoconf \
        gcc \
        make \
        rsync \
        libpng12-dev \
        libmcrypt-dev \
        libxml2-dev \
        libssl-dev \
        curl \
	"

RUN extensions=" \
        gd \
        mysqli \
        soap \
        mcrypt \
        mbstring \
    "; \
    apt-get update && apt-get install -y --no-install-recommends $extensionDeps \
    && docker-php-ext-install $extensions \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false $extensionDeps \
    && apt-get autoremove

ENV peclDeps=" \
    curl \
    libssl-dev \
    libxml2-dev \
    ca-certificates \
    make \
    autoconf \
    gcc \
    "

RUN apt-get update && apt-get install -y --no-install-recommends $peclDeps \
    && pecl install memcache && echo extension=memcache.so > $PHP_INI_DIR/conf.d/ext-memcache.ini \
    && pecl install redis && echo extension=redis.so > $PHP_INI_DIR/conf.d/ext-redis.ini \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false $peclDeps \
    && apt-get autoremove

MAINTAINER 10up

WORKDIR /var/www/html
COPY php-fpm.conf /usr/local/etc/

EXPOSE 9000
CMD ["php-fpm"]
