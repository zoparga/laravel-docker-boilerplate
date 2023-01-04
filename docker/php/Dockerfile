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
   # php8 \
   # php8.0-fpm \
   # php8.0-common \
   # php8.0-pdo \
   # php8.0-pdo_mysql \
   # php8.0-mysqli \
   # php8.0-mcrypt \
   # php8.0-mbstring \
   # php8.0-xml \
   # php8.0-openssl \
   # php8.0-json \
   # php8.0-phar \
   # php8.0-zip \
   # php8.0-gd \
   # php8.0-dom \
   # php8.0-session \
   # php8.0-zlib \
   # php8.0-soap \
   graphviz \
   libzip-dev \
   zip \
   icu-dev \
   icu-libs \
   mysql-client

# Install extensions
RUN docker-php-ext-install pdo pdo_mysql
RUN docker-php-ext-enable pdo_mysql


#Added to php-ext-gd requirement
RUN apk add libpng libpng-dev libjpeg-turbo-dev libwebp-dev zlib-dev libxpm-dev gd && docker-php-ext-install gd

RUN docker-php-ext-configure gd --with-jpeg
RUN docker-php-ext-install -j$(nproc) gd

RUN docker-php-ext-install exif
RUN docker-php-ext-enable exif
#RUN docker-php-ext-install gd
#RUN docker-php-ext-enable gd

# Added to php-ext-soap requirement
RUN apk add libxml2-dev && docker-php-ext-install soap

RUN docker-php-ext-enable soap

#install some base extensions
RUN docker-php-ext-install zip

#install intl package
RUN docker-php-ext-install intl
RUN docker-php-ext-enable intl

# imagick
RUN apk add --update --no-cache autoconf g++ imagemagick-dev libtool make pcre-dev \
   && pecl install imagick \
   && docker-php-ext-enable imagick \
   && apk del autoconf g++ libtool make pcre-dev

# Add node to container
#RUN apk add --update nodejs nodejs-npm
RUN apk add --update nodejs npm

# RUN php memory limit to -1
RUN cd /usr/local/etc/php/conf.d/ && \
   echo 'memory_limit = -1' >> /usr/local/etc/php/conf.d/docker-php-memlimit.ini

# install xdebug and enable it if the development environment is local
#RUN if [ $APP_ENV = "local" ]; then \
#    pecl install xdebug; \
#    docker-php-ext-enable xdebug; \
#    fi;

# Add webp
#RUN apk add --no-cache --update libpng-dev libjpeg-turbo-dev giflib-dev tiff-dev autoconf automake make gcc g++ wget && \
#  wget --no-check-certificate https://storage.googleapis.com/downloads.webmproject.org/releases/webp/libwebp-1.0.0.tar.gz && \
#  tar -xvzf libwebp-1.0.0.tar.gz && \
#  cd libwebp-1.0.0 && \
#  ./configure && \
#  make && \
#  make install && \
#  cd .. && \
#  rm -rf libwebp-1.0.0 libwebp-1.0.0.tar.gz

# Install PHP Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Remove Cache
RUN rm -rf /var/cache/apk/*

# Add UID '1000' to www-data
RUN apk add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data

# Set proper timezone
RUN apk --update add tzdata && \
   cp /usr/share/zoneinfo/Europe/Budapest /etc/localtime && echo Europe/Budapest > /etc/timezone && \
   apk del tzdata


# RUN apk add libreoffice

# RUN apk --no-cache add msttcorefonts-installer fontconfig && \
#    update-ms-fonts && \
#    fc-cache -f

# RUN set -ex \ 
#    && apk add --no-cache --virtual .phpize-deps $PHPIZE_DEPS imagemagick-dev libtool \
#    //&& export CFLAGS="$PHP_CFLAGS" CPPFLAGS="$PHP_CPPFLAGS" LDFLAGS="$PHP_LDFLAGS" \ 
#    && pecl install imagick-3.4.3 \
#    && docker-php-ext-enable imagick \ && apk add --no-cache --virtual .imagick-runtime-deps imagemagick \ 
#    && apk del .phpize-deps

#RUN apk --no-cache add php8.1-pecl-imagick

RUN apk add --no-cache ffmpeg

# Copy existing application directory permissions
COPY --chown=www-data:www-data . $REMOTE_WORKING_DIR

# Change current user to www
USER www-data

# Expose port 9000 and start php-fpm server
EXPOSE 9000



# Run php-fpm
CMD ["php-fpm"]


