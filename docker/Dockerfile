# Stage 1: Build Dependencies
FROM php:8.3-fpm-alpine AS build
WORKDIR /var/www

# Install system dependencies
RUN apk add --no-cache \
    git curl libpng-dev libonig-dev libxml2-dev zip unzip

# Install PHP extensions (MySQL, BCMath, Intl, GD for Filament)
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd intl

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Copy composer files first (Caching)
COPY composer.json composer.lock ./
RUN composer install --no-dev --no-scripts --no-autoloader --prefer-dist

# Copy App Source
COPY . .
RUN composer dump-autoload --optimize

# Stage 2: Production Runtime
FROM php:8.3-fpm-alpine
WORKDIR /var/www

# Runtime libraries
RUN apk add --no-cache libpng libjpeg-turbo freetype icu-libs

# Install extensions
RUN docker-php-ext-install pdo_mysql gd intl opcache

# Copy built app
COPY --from=build /var/www .

# Permissions (Crucial for Laravel)
RUN chown -R www-data:www-data /var/www/storage /var/www/bootstrap/cache

EXPOSE 9000
CMD ["php-fpm"]
