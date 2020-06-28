# PHP Version environment variable
ARG PHP_VERSION

# PHP Version alpine image to install based on the PHP_VERSION environment variable
FROM php:$PHP_VERSION-fpm-alpine

# Application environment variable
ARG APP_ENV

# Remote working directory environment variable
ARG REMOTE_WORKING_DIR

# Install Additional dependencies
RUN apk update && apk add --no-cache $PHPIZE_DEPS \
    build-base shadow nano curl gcc git bash \
    php7 \
    php7-fpm \
    php7-common \
    php7-pdo \
    php7-pdo_mysql \
    php7-mysqli \
    php7-mcrypt \
    php7-mbstring \
    php7-xml \
    php7-openssl \
    php7-json \
    php7-phar \
    php7-zip \
    php7-gd \
    php7-dom \
    php7-session \
    php7-zlib \
    php7-soap 

# Install extensions
RUN docker-php-ext-install pdo pdo_mysql
RUN docker-php-ext-enable pdo_mysql

#Added to php-ext-gd requirement
RUN apk add libpng libpng-dev libjpeg-turbo-dev libwebp-dev zlib-dev libxpm-dev gd && docker-php-ext-install gd

#RUN docker-php-ext-install gd
#RUN docker-php-ext-enable gd

# Added to php-ext-soap requirement 
RUN apk add libxml2-dev && docker-php-ext-install soap

RUN docker-php-ext-enable soap

# RUN php memory limit to -1
RUN cd /usr/local/etc/php/conf.d/ && \
  echo 'memory_limit = -1' >> /usr/local/etc/php/conf.d/docker-php-memlimit.ini

# install xdebug and enable it if the development environment is local
RUN if [ $APP_ENV = "local" ]; then \
    pecl install xdebug; \
    docker-php-ext-enable xdebug; \
    fi;

# Install PHP Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Remove Cache
RUN rm -rf /var/cache/apk/*

# Add UID '1000' to www-data
RUN apk add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data

# Copy existing application directory permissions
COPY --chown=www-data:www-data . $REMOTE_WORKING_DIR

# Change current user to www
USER www-data

# Expose port 9000 and start php-fpm server
EXPOSE 9000

# Run php-fpm
CMD ["php-fpm"]