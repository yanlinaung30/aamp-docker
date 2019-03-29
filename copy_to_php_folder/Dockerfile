ARG PHP_VERSION=""
## FROM composer:1.8.4 AS composer
FROM php:${PHP_VERSION:+${PHP_VERSION}-}fpm-alpine
## ENV COMPOSER_ALLOW_SUPERUSER 1 

## COPY --from=composer /usr/bin/composer /usr/bin/composer

RUN curl -sS https://getcomposer.org/installer | \
    php -- --install-dir=/usr/bin/ --filename=composer

RUN apk update; \
    apk upgrade; \
    apk add --no-cache python make g++ && \
    apk add --no-cache nodejs nodejs-npm && \
    apk add vips-dev fftw-dev icu-dev --no-cache --repository http://dl-3.alpinelinux.org/alpine/edge/testing --repository http://dl-3.alpinelinux.org/alpine/edge/main && \
    apk add php7-pecl-apcu --repository http://dl-3.alpinelinux.org/alpine/edge/community && \
    rm -fR /var/cache/apk/*

RUN docker-php-ext-configure intl
RUN docker-php-ext-install mysqli pdo pdo_mysql intl zip

RUN npm install -g yarn

WORKDIR /var/www/html
COPY package.json ./
COPY gulpfile.js ./
RUN yarn install && yarn run build && yarn cache clean

COPY composer.json ./
COPY composer.lock ./
RUN composer install --no-scripts --no-autoloader

COPY . ./
RUN chmod 777 -R html

RUN composer dump-autoload --optimize && \
    composer run-script post-install-cmd