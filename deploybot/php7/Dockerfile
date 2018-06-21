from php:7.0.13-cli

RUN cd /tmp && \
	/usr/bin/curl -sL -o setup_6.x https://deb.nodesource.com/setup_6.x && \
	bash setup_6.x && \
	/usr/bin/apt-get update && \
	/usr/bin/apt-get install -y bzip2 rubygems rubygems-integration inotify-tools nodejs git zlib1g-dev libbz2-dev libpng12-dev libjpeg-dev && \
	npm -g install yarn gulp grunt grunt-cli grunt-sass node-sass && \
	docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr && \
	docker-php-ext-install zip bz2 gd

# Install Sass
RUN gem install sass -v 3.4.23

# Register the COMPOSER_HOME environment variable
ENV COMPOSER_HOME /composer

# Add global binary directory to PATH and make sure to re-export it
ENV PATH /composer/vendor/bin:$PATH

# Allow Composer to be run as root
ENV COMPOSER_ALLOW_SUPERUSER 1

# Setup the Composer installer
RUN curl -o /tmp/composer-setup.php https://getcomposer.org/installer \
  && curl -o /tmp/composer-setup.sig https://composer.github.io/installer.sig \
  && php -r "if (hash('SHA384', file_get_contents('/tmp/composer-setup.php')) !== trim(file_get_contents('/tmp/composer-setup.sig'))) { unlink('/tmp/composer-setup.php'); echo 'Invalid installer' . PHP_EOL; exit(1); }"

ENV COMPOSER_VERSION 1.3.1
# Install Composer
RUN php /tmp/composer-setup.php --no-ansi --install-dir=/usr/local/bin --filename=composer --version=${COMPOSER_VERSION} && rm -rf /tmp/composer-setup.php

RUN mkdir ~/.ssh && \
    chmod 600 ~/.ssh && \
    touch ~/.ssh/known_hosts && \
    chmod 600 ~/.ssh/known_hosts && \
    ssh-keyscan github.com >> ~/.ssh/known_hosts && \
    ssh-keyscan 10up.git.beanstalkapp.com >> ~/.ssh/known_hosts

COPY ssh_config /root/.ssh/config
RUN chmod 600 /root/.ssh/config
