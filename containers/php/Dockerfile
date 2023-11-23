FROM --platform=linux/x86-64 php:7.4-fpm

# Set working directory
WORKDIR /var/www

# Prepare for node 14
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    curl \
    libxml2-dev \
    git \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    libicu-dev \
    libpng-dev \
    jpegoptim optipng pngquant gifsicle \
    locales \
    nano \
    nodejs \
    unzip \
    wget \
    zip \
    libgeoip-dev \
    geoip-bin \
    geoip-database \
    libmagickcore-dev \
    libmagickwand-dev \
    libldap-dev \
    procps \
    && pecl install xdebug-3.1.6 \
    && pecl install -o -f redis \
    && rm -rf /tmp/pear

RUN apt-get install -y \
    libnss3 \
    libnspr4 \
    libatk-adaptor \
    libxshmfence-dev \
    libatspi2.0-0 \
    libasound2-dev \
    libcairo2 \
    libpango-1.0-0 \
    libxfixes-dev \
    libxdamage-dev \
    libxcomposite-dev \
    libxkbcommon-x11-0 \
    libdbus-1-dev \
    libdrm-dev \
    libcups2-dev \
    libatk-bridge2.0-0 \
    libnspr4 \
    libgbm-dev \
    libatk1.0-0 \
    libxfixes-dev \
    libxrandr-dev \
    libpango-1.0 \
    libxslt-dev \
    libc-client-dev \
    libkrb5-dev \
    ldb-tools \
    libmemcached-dev \
    libssh2-1-dev \
    libssh2-1 \
    libncurses5 \
    procps

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/* && apt-get autoremove

# Install extensions
RUN docker-php-ext-enable xdebug \
    && docker-php-ext-enable redis \
    && docker-php-ext-install pdo_mysql exif pcntl calendar mysqli intl bcmath \
    && docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ \
    && docker-php-ext-configure soap --enable-soap \
    && docker-php-ext-install -j$(nproc) gd \
        xsl \
        gettext \
        bcmath \
        intl \
        soap \
        shmop \
        sysvmsg \
        sysvsem \
        sysvshm \
        sockets


RUN pecl install msgpack
RUN docker-php-ext-enable msgpack

# imap
RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install -j$(nproc) imap \
    && docker-php-ext-enable imap

# igbinary
RUN pecl install igbinary
RUN rm -rf /tmp/pear
RUN docker-php-ext-enable igbinary

# memcached
RUN pecl install memcached
RUN docker-php-ext-enable memcached

# apcu
RUN pecl install apcu
RUN docker-php-ext-enable apcu

# wddx
RUN wget https://github.com/php/pecl-text-wddx/archive/master.zip -O wddx.zip
RUN unzip wddx.zip && cd pecl-text-wddx-master && phpize && ./configure && make && make install

# openssl

RUN wget https://www.openssl.org/source/openssl-1.0.2o.tar.gz
RUN tar -xzf openssl-1.0.2o.tar.gz && cd openssl-1.0.2o && ./config --prefix=/usr/local/ssl --openssldir=/usr/local/ssl shared zlib && make && make install
RUN ln -s /var/www/openssl-1.0.2o/libssl.so.1.0.0 /lib/x86_64-linux-gnu/libssl.so.10
RUN ln -s /var/www/openssl-1.0.2o/libcrypto.so.1.0.0 /lib/x86_64-linux-gnu/libcrypto.so.10

# ssh
RUN wget https://pecl.php.net/get/ssh2-1.2.tgz
RUN tar -xzf ssh2-1.2.tgz && cd ssh2-1.2 && phpize &&  ./configure && make && make install

# ldap
RUN docker-php-ext-install ldap
RUN docker-php-ext-enable ldap

# imagick
RUN pecl install imagick
RUN docker-php-ext-enable imagick

# geoip
RUN pecl install http://pecl.php.net/get/geoip-1.1.1.tgz
RUN docker-php-ext-enable geoip

RUN npm install npm@latest -g

RUN npm install -g gulp && npm link gulp

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Add user for application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

RUN chmod -R 775 /var/www
RUN chown -R :1000 /var/www

# Change current user to www
USER www

# Expose port 9000 and start php-fpm server
EXPOSE 9000
CMD ["php-fpm"]


