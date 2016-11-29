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

ENV GPG_KEYS 0B96609E270F565C13292B24C13C70B87267B52D 0A95E9A026542D53835E3F3A7DEC4E69FC9C83D7 0E604491
RUN set -xe \
	&& for key in $GPG_KEYS; do \
		gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
	done

# compile openssl, otherwise --with-openssl won't work
RUN OPENSSL_VERSION="1.0.2e" \
      && cd /tmp \
      && mkdir openssl \
      && curl -sL "https://www.openssl.org/source/openssl-$OPENSSL_VERSION.tar.gz" -o openssl.tar.gz \
      && curl -sL "https://www.openssl.org/source/openssl-$OPENSSL_VERSION.tar.gz.asc" -o openssl.tar.gz.asc \
      && gpg --verify openssl.tar.gz.asc \
      && tar -xzf openssl.tar.gz -C openssl --strip-components=1 \
      && cd /tmp/openssl \
      && ./config && make && make install \
      && rm -rf /tmp/*

ENV PHP_VERSION 5.3.29
ENV PHP_FILENAME php-5.3.29.tar.xz

# php 5.3 needs older autoconf
# --enable-mysqlnd is included below because it's harder to compile after the fact the extensions are (since it's a plugin for several extensions, not an extension in itself)
RUN buildDeps=" \
		autoconf2.13 \
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
		--with-mysqli=mysqlnd \
		--with-curl \
		--with-openssl=/usr/local/ssl \
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
		libmysqlclient-dev \
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

EXPOSE 9053
CMD ["php-fpm"]
