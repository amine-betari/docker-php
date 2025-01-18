# docker build --target app -t abetari/php-composer:1.0 -f ./php/Dockerfile .
FROM php:8.1-fpm-alpine as app
# alpine veut dire une image minimaliste avec un minimum de choses dedans
# Comme ça je peux installer ce que je veux


# Useful PHP extension installer image, copy binary into your container
# Useful to install Xdebug
COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/local/bin/


RUN set -eux; \
    install-php-extensions pdo pdo_mysql;
# RUN docker-php-ext-install pdo pdo_mysql

# docker build -t amine:php81 -f php/Dockerfile .



# allow super user - set this if you use Composer as a
# super user at all times like in docker containers
ENV COMPOSER_ALLOW_SUPERUSER=1

# Obtain composer using multi-stage build
# A multi-stage build is a Docker feature that allows you to use multiple FROM statements in a single Dockerfile
# to create intermediate containers.
# https://docs.docker.com/build/building/multi-stage/

# Copy the Composer binary from the official Composer image
# Copy only the needed files (e.g., binaries) from one stage to another to keep the final image small and clean.
COPY --from=composer:2.4 /usr/bin/composer /usr/bin/composer

#Here, we are copying only composer.json and composer.lock (instead of copying the entire source)
# right before doing composer install.
# This is enough to take advantage of docker cache and composer install will
# be executed only when composer.json or composer.lock have indeed changed!-
# https://medium.com/@softius/faster-docker-builds-with-composer-install-b4d2b15d0fff
COPY ./app/composer.* ./

# install
RUN composer install --prefer-dist --no-dev --no-scripts --no-progress --no-interaction

# copy application files to the working directory
COPY ./app .

# run composer dump-autoload --optimize
RUN composer dump-autoload --optimize


# Dev image
# This stage is meant to be target-built into a separate image
# https://docs.docker.com/develop/develop-images/multistage-build/#stop-at-a-specific-build-stage
# https://docs.docker.com/compose/compose-file/#target
FROM app as app_dev

# Xdebug has different modes / functionalities. We can default to 'off' and set to 'debug'
# when we run docker compose up if we need it
ENV XDEBUG_MODE=off

# Copy xdebug config file into container
COPY ./php/conf.d/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# Install xdebug
RUN set -eux; \
	install-php-extensions xdebug