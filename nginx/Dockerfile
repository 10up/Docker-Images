# Dockerfile written by 10up <sales@10up.com>
#
# Work derived from Nginx Dockerfile:
# https://github.com/dockerfile/nginx

# Pull base image.
FROM debian:wheezy

MAINTAINER Eric Mann "eric.mann@10up.com"

RUN apt-key adv --keyserver pgp.mit.edu --recv-keys 573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62
RUN echo "deb http://nginx.org/packages/mainline/debian/ wheezy nginx" >> /etc/apt/sources.list

ENV DEBIAN_FRONTEND noninteractive
ENV NGINX_VERSION 1.7.12-1~wheezy

# Install Nginx.
RUN \
  apt-get update && \
  apt-get install -y ca-certificates nginx=${NGINX_VERSION} && \
  rm -rf /var/lib/apt/lists/* && \
  echo "\ndaemon off;" >> /etc/nginx/nginx.conf && \
  chown -R www-data:www-data /var/lib/nginx

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log
RUN ln -sf /dev/stderr /var/log/nginx/error.log

# Define mountable directories.
VOLUME ["/etc/nginx/sites-enabled", "/etc/nginx/certs", "/etc/nginx/conf.d", "/var/log/nginx", "/var/www/html", "/var/cache/nginx"]

# Define working directory.
WORKDIR /etc/nginx

# Expose ports.
EXPOSE 80 443

# Define default command.
CMD ["nginx"]