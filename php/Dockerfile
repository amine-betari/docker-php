FROM php:8.1-fpm-alpine
# alpine veut dire une image minimaliste avec un minimum de choses dedans
# Comme ça je peux installer ce que je veux
RUN docker-php-ext-install pdo pdo_mysql

# docker build -t amine:php81 -f php/Dockerfile .