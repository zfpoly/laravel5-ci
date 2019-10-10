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
RUN cat > /usr/local/bin/install-mysql.sh << EOM
#!/bin/sh

apt-get update && apt-get install -y --no-install-recommends gnupg dirmngr && rm -rf /var/lib/apt/lists/*

{ \\
  echo mysql-community-server mysql-community-server/data-dir select ''; \\
  echo mysql-community-server mysql-community-server/root-pass password ''; \\
  echo mysql-community-server mysql-community-server/re-root-pass password ''; \\
  echo mysql-community-server mysql-community-server/remove-test-db select false; \\
} | debconf-set-selections \\


set -x \\
        && apt-get update && apt-get install -y --no-install-recommends ca-certificates wget && rm -rf /var/lib/apt/lists/* \\
        && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/1.7/gosu-$(dpkg --print-architecture)" \\
        && wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/1.7/gosu-$(dpkg --print-architecture).asc" \\
        && export GNUPGHOME="$(mktemp -d)" \\
        && gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \\
        && gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu \\
        && gpgconf --kill all \\
        && rm -rf "$GNUPGHOME" /usr/local/bin/gosu.asc \\
        && chmod +x /usr/local/bin/gosu \\
        && gosu nobody true \\
        && apt-get purge -y --auto-remove ca-certificates wget

set -ex; \\
# gpg: key 5072E1F5: public key "MySQL Release Engineering <mysql-build@oss.oracle.com>" imported
        key='A4A9406876FCBD3C456770C88C718D3B5072E1F5'; \\
        export GNUPGHOME="$(mktemp -d)"; \\
        gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \\
        gpg --batch --export "$key" > /etc/apt/trusted.gpg.d/mysql.gpg; \\
        gpgconf --kill all; \\
        rm -rf "$GNUPGHOME"; \\
        apt-key list > /dev/null

echo "deb http://repo.mysql.com/apt/debian/ buster mysql-5.7" > /etc/apt/sources.list.d/mysql.list

apt-get update && apt-get install -y mysql-server=5.7.27-1debian10
EOM

RUN chmod +x /usr/local/bin/install-mysql.sh
RUN /usr/local/bin/install-mysql.sh

#Install laravel envoy
RUN composer global require "laravel/envoy=~1.0"
