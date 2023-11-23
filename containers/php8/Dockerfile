FROM --platform=linux/x86-64 php:8.2-fpm

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
    procps \
    sudo \
    cron \
    mc \
    && apt-get clean && rm -rf /var/lib/apt/lists/* && apt-get autoremove

# Install pecl extensions
RUN pecl install msgpack \
    xdebug \
    -o -f redis \
    igbinary \
    memcached \
    apcu \
    imagick \
    ssh2-1.3.1 \
    && rm -rf /tmp/pear

# Install extensions
RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ \
    && docker-php-ext-configure soap --enable-soap \
    && docker-php-ext-install  -j$(nproc) imap \
        pdo_mysql exif pcntl calendar mysqli intl bcmath \
        xsl \
        gettext \
        bcmath \
        intl \
        soap \
        shmop \
        sysvmsg \
        sysvsem \
        sysvshm \
        sockets \
        ldap \
    && docker-php-ext-enable  imap \
        msgpack \
        igbinary \
        memcached \
        apcu \
        ldap \
        imagick \
        xdebug \
        redis \
        ssh2

# wddx
RUN cd /var/opt \
    && wget https://github.com/php/pecl-text-wddx/archive/master.zip -O wddx.zip \
    && unzip wddx.zip && cd pecl-text-wddx-master && phpize && ./configure && make && make install \
    && rm /var/opt/wddx.zip

# geoip
RUN cd /var/opt \
    && wget https://github.com/rlerdorf/geoip/archive/refs/heads/main.zip  -O geoip.zip \
    && unzip geoip.zip && cd geoip-main && phpize && ./configure && make && make install \
    && docker-php-ext-enable geoip \
    && npm install npm@latest -g \
    && npm install -g gulp && npm link gulp \
    && rm /var/opt/geoip.zip

# openssl
RUN cd /var/opt \
    && wget https://www.openssl.org/source/openssl-1.0.2o.tar.gz -O openssl-1.0.2o.tar.gz \
    && tar -xzf openssl-1.0.2o.tar.gz && cd openssl-1.0.2o && ./config --prefix=/usr/local/ssl --openssldir=/usr/local/ssl shared zlib && make && make install \
    && ln -s /var/opt/openssl-1.0.2o/libssl.so.1.0.0 /lib/x86_64-linux-gnu/libssl.so.10 \
    && ln -s /var/opt/openssl-1.0.2o/libcrypto.so.1.0.0 /lib/x86_64-linux-gnu/libcrypto.so.10 \
    && rm /var/opt/openssl-1.0.2o.tar.gz

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Use the default development PHP configuration
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

# Add user for application
RUN groupadd -g 1000 www \
    && useradd -u 1000 -ms /bin/bash -g www www \
    && chmod -R 775 /var/www \
    && chown -R :1000 /var/www

# Configure sudo
RUN echo 'www ALL= (ALL) NOPASSWD:ALL'> /etc/sudoers.d/www \
    && chmod 644 /etc/sudoers.d/www

# Change current user to www
USER www

# Expose port 9000 and start php-fpm server
EXPOSE 9000
CMD ["php-fpm"]
