FROM php:8.1-fpm-alpine
# alpine veut dire une image minimaliste avec un minimum de choses dedans
# Comme ça je peux installer ce que je veux
RUN docker-php-ext-install pdo pdo_mysql

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