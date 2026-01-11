# PHP Version environment variable
ARG PHP_VERSION
# Node Version environment variable
ARG NODE_VERSION
# Application environment variable
ARG APP_ENV_DOCKER
# Remote working directory environment variable
ARG REMOTE_WORKING_DIR

FROM node:${NODE_VERSION}-alpine AS node

# PHP Version alpine image to install based on the PHP_VERSION environment variable
FROM php:${PHP_VERSION}-fpm-alpine

# Install OpenSSL first to ensure SSL libraries are available before any operations
RUN apk add --no-cache openssl ca-certificates

# Copy Node binaries selectively (avoid overwriting system libraries like SSL)
COPY --from=node /usr/local/bin/node /usr/local/bin/node
COPY --from=node /usr/local/bin/npm /usr/local/bin/npm
COPY --from=node /usr/local/bin/npx /usr/local/bin/npx
COPY --from=node /usr/local/lib/node_modules /usr/local/lib/node_modules

# Install Additional dependencies (build tools and runtime deps separately for better caching)
RUN apk update && apk add --no-cache --virtual .build-deps \
   build-base \
   gcc \
   autoconf \
   g++ \
   libtool \
   make \
   pcre-dev \
   libzip-dev \
   libpng-dev \
   libjpeg-turbo-dev \
   libwebp-dev \
   freetype-dev \
   zlib-dev \
   libxpm-dev \
   imagemagick-dev \
   libxml2-dev \
   icu-dev && \
   apk add --no-cache \
   shadow \
   nano \
   curl \
   git \
   bash \
   graphviz \
   zip \
   icu-libs \
   mysql-client \
   libpng \
   gd \
   supervisor \
   ffmpeg

# Install PHP extensions (combined for better caching)
RUN docker-php-ext-install pdo pdo_mysql && \
   docker-php-ext-configure gd --with-jpeg --with-webp --with-freetype && \
   docker-php-ext-install -j$(nproc) gd exif soap zip intl && \
   pecl install imagick && \
   docker-php-ext-enable pdo_mysql imagick exif soap zip intl && \
   apk del .build-deps

# Configure PHP and install Composer (combined for efficiency)
RUN echo 'memory_limit = -1' > /usr/local/etc/php/conf.d/docker-php-memlimit.ini && \
   curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
   apk --update add --no-cache tzdata && \
   cp /usr/share/zoneinfo/Europe/Budapest /etc/localtime && \
   echo Europe/Budapest > /etc/timezone && \
   apk del tzdata && \
   usermod -u 1000 www-data && \
   groupmod -g 1000 www-data && \
   rm -rf /tmp/* /var/cache/apk/*

# Copy MySQL client config to disable SSL (needed for mysqldump with MySQL 8.0+)
COPY config/my.cnf /etc/my.cnf

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


