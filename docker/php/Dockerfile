ARG PHP_VERSION=8.2

# 1) build stage
FROM composer:2 AS vendor
WORKDIR /app
COPY composer.json composer.lock ./
RUN composer install --no-dev --prefer-dist --no-interaction --no-progress

# 2) runtime stage
FROM php:${PHP_VERSION}-fpm
RUN apt-get update && apt-get install -y \
    libzip-dev libonig-dev libxml2-dev \
  && docker-php-ext-install pdo_mysql mbstring xml zip \
  && rm -rf /var/lib/apt/lists/*

WORKDIR /var/www
COPY --from=vendor /app/vendor /var/www/vendor
COPY . /var/www

RUN php artisan config:cache || true \
 && php artisan route:cache || true \
 && php artisan view:cache  || true
