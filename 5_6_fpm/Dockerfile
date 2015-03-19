FROM debian:wheezy

EXPOSE 9000

RUN apt-get update && apt-get -y install \
  wget \
  && echo "deb http://packages.dotdeb.org wheezy-php56 all" >> /etc/apt/sources.list.d/dotdeb.list \
  && echo "deb-src http://packages.dotdeb.org wheezy-php56 all" >> /etc/apt/sources.list.d/dotdeb.list \
  && wget http://www.dotdeb.org/dotdeb.gpg -O- |apt-key add - \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get -y purge wget

RUN apt-get update && apt-get -y install \
  php-pear \
  php5-memcache \
  php5-mysql \
  php5-fpm \
  php5-gd \
  php5-mcrypt \
  && pecl install redis \
  && echo "extension=redis.so" > /etc/php5/cli/php.ini \
  && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false php5-dev php-pear \
  && rm -rf /var/lib/apt/lists/*

MAINTAINER Taylor Lovett