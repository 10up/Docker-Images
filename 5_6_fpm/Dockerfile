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
  php5-memcache \
  php5-mysql \
  php5-fpm \
  php5-gd \
  php5-mcrypt \
  php5-redis \
  && apt-get -y autoremove \
  && rm -rf /var/lib/apt/lists/*

MAINTAINER Taylor Lovett