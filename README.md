# docker-php


```Dockerfile
FROM buzzxu/php:7.2-fpm

COPY . /var/www/html/

RUN set -x  \
    && mkdir -p /var/www/html/runtime \
    && chown -R www-data:www-data /var/www/html/runtime

EXPOSE 9000

```