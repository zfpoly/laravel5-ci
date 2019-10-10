FROM php:7.1

# Install modules
RUN buildDeps="libpq-dev libzip-dev libicu-dev libfreetype6-dev libmagickwand-6.q16-dev" && \
    apt-get update && \
    apt-get install -y $buildDeps --no-install-recommends && \
    ln -s /usr/lib/x86_64-linux-gnu/ImageMagick-6.8.9/bin-Q16/MagickWand-config /usr/bin && \
    pecl install imagick && \
    echo "extension=imagick.so" > /usr/local/etc/php/conf.d/ext-imagick.ini && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-install \
    opcache \
    bcmath \
    bz2 \
    calendar \
    exif \
    gettext \
    iconv \
    pcntl \
    pdo \
    pdo_mysql \
    pdo_pgsql \
    pgsql \
    sockets \
    shmop \
    intl \
    zip \
    && pecl install apcu \
    && docker-php-ext-enable apcu

RUN apt-get update \
&& apt-get install -y libwebp-dev libjpeg62-turbo-dev libpng-dev libxpm-dev \
    libfreetype6-dev

# Install gd
RUN docker-php-ext-configure gd --with-gd --with-webp-dir --with-jpeg-dir \
    --with-png-dir --with-zlib-dir --with-xpm-dir --with-freetype-dir

RUN docker-php-ext-install gd

#Install composer
RUN curl --silent --show-error https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

#Install mysql client
COPY ./install-mysql.sh /usr/local/bin/install-mysql
RUN chmod +x /usr/local/bin/install-mysql
RUN /usr/local/bin/install-mysql

#Install laravel envoy
RUN composer global require "laravel/envoy=~1.0"
