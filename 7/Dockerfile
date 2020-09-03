FROM php:7-fpm

LABEL MAINTAINER buzzxu <downloadxu@163.com>

ENV PHP_OPCACHE_VALIDATE_TIMESTAMPS="0" \
    PHP_OPCACHE_MEMORY_CONSUMPTION="192"

RUN echo \
    deb http://mirrors.163.com/debian/ buster main non-free contrib \
    deb http://mirrors.163.com/debian/ buster-updates main non-free contrib \
    deb http://mirrors.163.com/debian/ buster-backports main non-free contrib \
    deb http://mirrors.163.com/debian-security/ buster/updates main non-free contrib \
    deb-src http://mirrors.163.com/debian/ buster main non-free contrib \
    deb-src http://mirrors.163.com/debian/ buster-updates main non-free contrib \
    deb-src http://mirrors.163.com/debian/ buster-backports main non-free contrib \
    deb-src http://mirrors.163.com/debian-security/ buster/updates main non-free contrib \
    > /etc/apt/sources.list

COPY conf/fpm-pool.conf /etc/php7/php-fpm.d/www.conf
COPY conf/php.ini /usr/local/etc/php/php.ini

ENV DEBIAN_FRONTEND noninteractive

RUN set -ex; \
    \
    savedAptMark="$(apt-mark showmanual)"; \
    \
    apt-get update && apt-get install -yq --no-install-recommends --no-install-suggests \
        apt-utils \
        libbz2-dev \
        libfreetype6-dev \
        libjpeg-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libwebp-dev \
        libxpm-dev \
        gnupg2 \
        libicu-dev \ 
        libxml2-dev \ 
        libxslt-dev \ 
        libonig-dev \
        libgmp-dev \ 
        libzip-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install -j$(nproc) mysqli pdo pdo_mysql opcache bz2 zip\
    && docker-php-ext-install -j$(nproc) dom xmlrpc xsl \
    && docker-php-ext-install -j$(nproc) gettext mbstring \
    && docker-php-ext-install -j$(nproc) iconv \
    && docker-php-ext-configure intl \ 
    && docker-php-ext-install -j$(nproc) intl \
    && ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h \
    && docker-php-ext-install -j$(nproc) gmp bcmath \
    && pecl install redis \
    && docker-php-ext-enable redis \
    && rm /etc/localtime \
    && ln -sv /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \ 
    && echo "Asia/Shanghai" > /etc/timezone \
    && \
    { \
        echo 'opcache.enable=1'; \
        echo 'opcache.memory_consumption=${PHP_OPCACHE_MEMORY_CONSUMPTION}'; \
        echo 'opcache.interned_strings_buffer=16'; \
        echo 'opcache.max_accelerated_files=7963'; \
        echo 'opcache.max_wasted_percentage=10'; \
        echo 'opcache.revalidate_freq=0'; \
        echo 'opcache.validate_timestamps=${PHP_OPCACHE_VALIDATE_TIMESTAMPS}'; \
        echo 'opcache.fast_shutdown=1'; \
    } > $PHP_INI_DIR/conf.d/opcache-recommended.ini; \
    \
    {\
        echo 'session.cookie_httponly = 1'; \
        echo 'session.use_strict_mode = 1'; \
    } > $PHP_INI_DIR/conf.d/session-strict.ini; \
    sed -i 's/127.0.0.1:9000/0.0.0.0:9000/g' /etc/php7/php-fpm.d/www.conf \
    && chown -R www-data:www-data /var/www \
    && chmod -R 755 /var/www/html \
    # Clean up
    && apt-get clean \
    && apt-get autoremove \
    && rm -rf /etc/nginx/conf.d/default.conf \
    && apt-get purge -y --auto-remove $buildDeps \
    && rm -rf /var/lib/apt/lists/*

ENV TZ=Asia/Shanghai
ENV LANG='C.UTF-8' LC_ALL='C.UTF-8'

EXPOSE 9000


