FROM php:7.4.3-fpm-alpine3.11 as base

RUN apk update \
    && apk add --no-cache nginx supervisor

# Remove default server definition
RUN mkdir -p /run/nginx

COPY ./config/nginx.conf /etc/nginx/conf.d
COPY ./config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY ./config/stop-supervisor.sh /
COPY ./config/fpm-pool.conf /usr/local/etc/php-fpm.d/www.conf
RUN rm /etc/nginx/conf.d/default.conf

WORKDIR /app

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]


FROM base as full

RUN apk update \
    && apk add --no-cache freetype-dev \
    libjpeg-turbo-dev \
    libpng-dev \
    libwebp-dev \
    jpegoptim \
    optipng \
    pngquant \
    gifsicle \
    zip \
    supervisor \
    git \
    nginx \
    openssl-dev  \
    libressl-dev  \
    curl-dev \
    yaml-dev

RUN apk add --no-cache --virtual .phpize-deps $PHPIZE_DEPS \
    && pecl install yaml \
    && pecl install redis  \
    && pecl install mongodb \
    && apk del .phpize-deps

RUN docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-enable redis mongodb yaml \
    && docker-php-ext-install mysqli pdo pdo_mysql

# Remove default server definition
RUN mkdir -p /run/nginx

COPY ./config/nginx.conf /etc/nginx/conf.d
COPY ./config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY ./config/stop-supervisor.sh /
COPY ./config/fpm-pool.conf /usr/local/etc/php-fpm.d/www.conf

WORKDIR /app

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]