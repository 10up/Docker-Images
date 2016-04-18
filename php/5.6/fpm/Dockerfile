# Dockerfile written by 10up <sales@10up.com>
#
# Work derived from official PHP Docker Library:
# Copyright (c) 2014-2015 Docker, Inc.

FROM debian:jessie

# persistent / runtime deps
RUN apt-get update && apt-get install -y ca-certificates curl libssl-dev libxml2-dev libxml2 libpng12-dev libmcrypt-dev php5-memcached --no-install-recommends && rm -r /var/lib/apt/lists/*

# phpize deps
RUN apt-get update && apt-get install -y autoconf file g++ gcc libc-dev make pkg-config re2c --no-install-recommends && rm -r /var/lib/apt/lists/*

ENV PHP_INI_DIR /usr/local/etc/php
RUN mkdir -p $PHP_INI_DIR/conf.d

ENV PHP_EXTRA_CONFIGURE_ARGS --enable-fpm --with-fpm-user=www-data --with-fpm-group=www-data

ENV GPG_KEYS 6E4F6AB321FDC07F2C332E3AC2BF0BC433CFC8B3 0BD78B5F97500D450838F95DFE857D9A90D90EC1
RUN set -xe \
	&& for key in $GPG_KEYS; do \
		gpg --keyserver pool.sks-keyservers.net --recv-keys "$key"; \
	done

ENV PHP_VERSION 5.6.20
ENV PHP_FILENAME php-5.6.20.tar.xz

RUN buildDeps=" \
            bzip2 \
            xz-utils \
            libreadline6-dev \
            libcurl4-openssl-dev \
    	" \
	&& set -x \
	&& apt-get update && apt-get install -y $buildDeps --no-install-recommends && rm -rf /var/lib/apt/lists/* \
	&& curl -SL "http://php.net/get/php-$PHP_VERSION.tar.bz2/from/this/mirror" -o "$PHP_FILENAME" \
	&& curl -SL "http://php.net/get/php-$PHP_VERSION.tar.bz2.asc/from/this/mirror" -o "$PHP_FILENAME.asc" \
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
	&& make clean \
	&& apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false $buildDeps \
	&& apt-get autoremove

COPY docker-php-ext-* /usr/local/bin/

ENV extensionDeps=" \
        rsync \
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
		libmemcached-dev \
	"
RUN apt-get update && apt-get install -y --no-install-recommends $peclDeps \
	&& pecl install redis && echo extension=redis.so > $PHP_INI_DIR/conf.d/ext-redis.ini \
	&& pecl install memcached && echo extension=memcached.so > $PHP_INI_DIR/conf.d/ext-memcached.ini \
	&& pecl install memcache && echo extension=memcache.so > $PHP_INI_DIR/conf.d/ext-memcache.ini \
#   && pecl install apcu && echo extension=apcu.so > $PHP_INI_DIR/conf.d/ext-apcu.ini \
	&& apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false $peclDeps \
	&& apt-get autoremove

MAINTAINER 10up

WORKDIR /var/www/html
COPY php-fpm.conf /usr/local/etc/

EXPOSE 9056
CMD ["php-fpm"]