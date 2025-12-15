FROM php:8.2-apache

# kup php libsov
RUN apt-get update && \
    apt-get install -y \
        unzip \
        git \
        curl \
        libzip-dev \
        libpng-dev \
        libjpeg-dev \
        libonig-dev \
        libxml2-dev \
        libssl-dev \
        pkg-config \
        libcurl4-openssl-dev \
        zlib1g-dev \
        libpq-dev \
        libicu-dev \
        libevent-dev \
        libz-dev \
        libreadline-dev \
        libxslt1-dev \
        libffi-dev \
        libgmp-dev \
        libedit-dev \
        libsqlite3-dev \
        libldb-dev \
        libkrb5-dev \
        libsasl2-dev \
        libldap2-dev \
        libmemcached-dev \
        && docker-php-ext-install mysqli pdo pdo_mysql && \
    pecl install redis && \
    docker-php-ext-enable redis && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# enable apache
RUN a2enmod rewrite

RUN git clone -b relative https://github.com/MatejBokal/devops-spletna /tmp/devops-spletna

RUN mkdir -p /var/www/html/taprav-fri/api && \
    cp -r /tmp/devops-spletna/code/taprav-fri/api /var/www/html/taprav-fri && \
    chown -R www-data:www-data /var/www/html/taprav-fri/api

RUN sed -i "s/define('DB_USER', 'root');/define('DB_USER', 'user');/" /var/www/html/taprav-fri/api/includes/config.php && \
    sed -i "s/define('DB_HOST', '127.0.0.1');/define('DB_HOST', 'mysql');/" /var/www/html/taprav-fri/api/includes/config.php && \
    sed -i "s/\$redis->connect('127\.0\.0\.1', 6379)/\$redis->connect('redis', 6379)/" /var/www/html/taprav-fri/api/includes/config.php

# forward 80
EXPOSE 80

# Start Apache in foreground
CMD ["apache2ctl", "-D", "FOREGROUND"]
