FROM quay.io/continuouspipe/ubuntu16.04:v1.0

MAINTAINER Kieren Evans <kieren.evans+cp-dockerfiles@inviqa.com>

ARG PHP_VERSION
ENV PHP_VERSION ${PHP_VERSION:-7.0}
RUN echo 'deb http://s3-eu-west-1.amazonaws.com/qafoo-profiler/packages debian main' > /etc/apt/sources.list.d/tideways.list \
 && curl -sS https://s3-eu-west-1.amazonaws.com/qafoo-profiler/packages/EEB5E8F4.gpg | apt-key add - \
 && if [ "$PHP_VERSION" != "7.0" ]; then \
   echo 'deb http://ppa.launchpad.net/ondrej/php/ubuntu xenial main' > /etc/apt/sources.list.d/php-ppa.list; \
   apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E5267A6C; \
 fi \
 && apt-get update -qq \
 && DEBIAN_FRONTEND=noninteractive apt-get -qq -y --no-install-recommends install \
    apache2 \
    apache2-utils \
    curl \
    git \
    "libapache2-mod-php$PHP_VERSION" \
    php-apcu \
    php-imagick \
    php-redis \
    "php$PHP_VERSION-curl" \
    "php$PHP_VERSION-gd" \
    "php$PHP_VERSION-intl" \
    "php$PHP_VERSION-mbstring" \
    "php$PHP_VERSION-mcrypt" \
    "php$PHP_VERSION-mysql" \
    "php$PHP_VERSION-xsl" \
    "php$PHP_VERSION-zip" \
 \
 # Clean the image \
 && apt-get auto-remove -qq -y \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* \
 \
 # Install composer for PHP dependencies - Syntax looks wrong but it works! \
 && php -r "copy('https://getcomposer.org/installer', '/tmp/composer-setup.php');" \
 && if [ "$(wget https://composer.github.io/installer.sig -O - -q)" = "$(php -r 'echo hash_file("SHA384", "/tmp/composer-setup.php");')" ]; \
    then \
      php /tmp/composer-setup.php --install-dir='/usr/local/bin/' --filename='composer' --quiet; \
      rm /tmp/composer-setup.php; \
    fi \
 \
 # Enable the correct config for most sites \
 && a2disconf other-vhosts-access-log \
 && a2enmod rewrite

USER build

RUN composer global require "hirak/prestissimo" --no-interaction --no-ansi --quiet --no-progress --prefer-dist \
 && composer clear-cache --no-ansi --quiet \
 && chmod -R go-w ~/.composer/vendor

USER root

# Add configuration
COPY ./etc/ /etc/
COPY ./usr/ /usr/

RUN find /etc/confd/conf.d/ -name "*.toml" -type f -exec sed -i'' "s/\$PHP_VERSION/$PHP_VERSION/" {} \;
