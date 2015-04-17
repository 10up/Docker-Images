# Dockerfile written by 10up <sales@10up.com>
#
# Work derived from Nginx Dockerfile:
# https://github.com/dockerfile/nginx

# Pull base image.
FROM debian:wheezy

MAINTAINER Eric Mann "eric.mann@10up.com"

ENV DEBIAN_FRONTEND noninteractive

# Install Nginx.
RUN \
  add-apt-repository -y ppa:nginx/stable && \
  apt-get update && \
  apt-get install -y ca-certificates nginx=${NGINX_VERSION} && \
  rm -rf /var/lib/apt/lists/* && \
  echo "\ndaemon off;" >> /etc/nginx/nginx.conf && \
  chown -R www-data:www-data /var/lib/nginx

# Define mountable directories.
VOLUME ["/etc/nginx/sites-enabled", "/etc/nginx/certs", "/etc/nginx/conf.d", "/var/log/nginx", "/var/www/html", "/var/cache/nginx"]

# Define working directory.
WORKDIR /etc/nginx

# Expose ports.
EXPOSE 80 443

# Define default command.
CMD ["nginx"]