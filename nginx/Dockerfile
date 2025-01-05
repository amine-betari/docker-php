FROM nginx:latest

COPY ./nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf

RUN mkdir -p /var/www/html/public

COPY ./app/public /var/www/html/public