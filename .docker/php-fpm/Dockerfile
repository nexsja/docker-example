FROM composer:1.9.1 AS composer
FROM php:7.4.0-fpm

# Let's use bash as a default shell
SHELL ["/bin/bash", "-c"]

# Update package list and install necessary libraries
RUN apt-get update && apt-get install -y \
    zlib1g-dev libzip-dev libxml2-dev libicu-dev libpq-dev g++ vim git unzip jq bash-completion iproute2 \
    && rm -rf /var/lib/apt/lists/*

# Enable all necessary PHP packages
RUN docker-php-ext-install -j$(nproc) bcmath \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo pdo_pgsql pgsql \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-install opcache \
    && docker-php-ext-install zip

#RUN docker-php-ext-install pecl install xdebug-2.8.0 \
#    && docker-php-ext-enable xdebug

# Install APCu and APC backward compatibility
RUN pecl install apcu \
    && pecl install apcu_bc-1.0.5 \
    && docker-php-ext-enable apcu --ini-name 10-docker-php-ext-apcu.ini \
    && docker-php-ext-enable apc --ini-name 20-docker-php-ext-apc.ini

# Copy the Composer PHAR from the Composer image into the PHP image
COPY --from=composer /usr/bin/composer /usr/bin/composer
# Update composer to latest version
RUN composer self-update

# Copy use php.ini to container
COPY ./.docker/php-fpm/php-dev.ini /usr/local/etc/php/php.ini

# Allow use of composer as a root, add necessary directories to `$PATH`
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV PATH "$PATH:/root/.composer/vendor/bin:/app/vendor/bin:/app/bin"

# Define used work directory
WORKDIR /app

# Add everything inside docker image
COPY . .

# Ensure that all required files has execute rights
RUN chmod +x /app/docker-entrypoint-dev.sh \
    && chmod +x /usr/bin/composer

# Add necessary stuff to bash autocomplete
RUN echo 'source /usr/share/bash-completion/bash_completion' >> /etc/bash.bashrc \
    && echo 'eval "$(symfony-autocomplete --shell bash)"' >> /etc/bash.bashrc

EXPOSE 9000

#ENTRYPOINT ["docker-entrypoint-dev.sh"]
