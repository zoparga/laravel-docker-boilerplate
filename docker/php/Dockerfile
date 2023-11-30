# PHP Version environment variable
ARG PHP_VERSION
# Node Version environment variable
ARG NODE_VERSION
# Application environment variable
ARG APP_ENV
# Remote working directory environment variable
ARG REMOTE_WORKING_DIR

FROM node:${NODE_VERSION}-alpine AS node

# PHP Version alpine image to install based on the PHP_VERSION environment variable
FROM php:$PHP_VERSION-fpm-alpine

COPY --from=node /usr/lib /usr/lib
COPY --from=node /usr/local/lib /usr/local/lib
COPY --from=node /usr/local/include /usr/local/include
COPY --from=node /usr/local/bin /usr/local/bin

# Install Additional dependencies
RUN apk update && apk add --no-cache \
   build-base \
   shadow \
   nano \
   curl \
   gcc \
   git \
   bash \
   graphviz \
   libzip-dev \
   zip \
   icu-dev \
   icu-libs \
   mysql-client \
   libpng \
   libpng-dev \
   libjpeg-turbo-dev \
   libwebp-dev \
   freetype-dev \
   zlib-dev \
   libxpm-dev \
   gd \
   autoconf \
   g++ \
   imagemagick-dev \
   libtool \
   make \
   pcre-dev \
   supervisor \
   ffmpeg \
   libxml2-dev


# Install extensions
RUN docker-php-ext-install pdo pdo_mysql
RUN docker-php-ext-enable pdo_mysql
RUN docker-php-ext-configure gd --with-jpeg --with-webp --with-freetype
RUN docker-php-ext-install -j$(nproc) gd
RUN pecl install imagick
RUN docker-php-ext-enable imagick
RUN docker-php-ext-install exif
RUN docker-php-ext-enable exif
RUN docker-php-ext-install soap
RUN docker-php-ext-enable soap
RUN docker-php-ext-install zip
RUN docker-php-ext-enable zip
RUN docker-php-ext-install intl
RUN docker-php-ext-enable intl

RUN apk del autoconf g++ libtool make pcre-dev

# RUN php memory limit to -1
RUN cd /usr/local/etc/php/conf.d/ && \
   echo 'memory_limit = -1' >> /usr/local/etc/php/conf.d/docker-php-memlimit.ini

# Install PHP Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Add UID '1000' to www-data
RUN apk add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data

# Set proper timezone
RUN apk --update add tzdata && \
   cp /usr/share/zoneinfo/Europe/Budapest /etc/localtime && echo Europe/Budapest > /etc/timezone && \
   apk del tzdata

# Remove Cache
RUN rm -rf /tmp/* /var/cache/apk/*

# # Copy local supervisord.conf to the conf.d directory
COPY --chown=www-data:www-data supervisord.conf /etc/supervisord.conf

# Copy existing application directory permissions
COPY --chown=www-data:www-data . $REMOTE_WORKING_DIR

# Change current user to www
USER www-data

# Expose port 9000 and start php-fpm server
EXPOSE 9000

# Run php-fpm
CMD ["php-fpm"]


