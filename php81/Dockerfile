FROM php:8.1-apache-buster

ARG DEBIAN_FRONTEND=noninteractive

# Install pickle
ENV PICKLE_VERSION=0.7.9
RUN curl -Lo /usr/local/bin/pickle https://github.com/FriendsOfPHP/pickle/releases/download/v$PICKLE_VERSION/pickle.phar && \
    chmod +x /usr/local/bin/pickle

# Update
RUN apt-get -y update --fix-missing && \
    apt-get upgrade -y && \
    apt-get --no-install-recommends install -y apt-utils && \
    rm -rf /var/lib/apt/lists/*

# Install useful tools and install important libaries
RUN apt-get -y update && \
    apt-get -y --no-install-recommends install nano wget \
    dialog \
    libsqlite3-dev \
    libsqlite3-0 && \
    apt-get -y --no-install-recommends install default-mysql-client \
    zlib1g-dev \
    libzip-dev \
    libicu-dev && \
    apt-get -y --no-install-recommends install --fix-missing apt-utils \
    build-essential \
    git \
    curl \
    libonig-dev && \ 
    apt-get -y --no-install-recommends install --fix-missing libcurl4 \
    libcurl4-openssl-dev \
    zip \
    ghostscript \
    openssl && \
    rm -rf /var/lib/apt/lists/* && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install xdebug
RUN pickle install xdebug && \
    docker-php-ext-enable xdebug

# Install redis
RUN pickle install redis && \
    docker-php-ext-enable redis

# Install imagick
RUN apt-get update && \
    apt-get -y --no-install-recommends install --fix-missing libmagickwand-dev && \
    rm -rf /var/lib/apt/lists/* && \
    pickle install imagick && \
    docker-php-ext-enable imagick

# Other PHP8 Extensions
RUN docker-php-ext-install pdo_mysql
RUN docker-php-ext-install pdo_sqlite
RUN docker-php-ext-install mysqli
RUN docker-php-ext-install curl
#RUN docker-php-ext-install tokenizer # does not work (yet)
#RUN docker-php-ext-install json # does not work (yet)
RUN docker-php-ext-install zip
RUN docker-php-ext-install -j$(nproc) intl
RUN docker-php-ext-install mbstring
RUN docker-php-ext-install gettext
RUN docker-php-ext-install calendar 
RUN docker-php-ext-install exif
RUN docker-php-ext-install opcache

# Install Freetype
RUN apt-get -y update && \
    apt-get --no-install-recommends install -y libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg && \
    docker-php-ext-install gd

# Enable apache modules
RUN a2enmod rewrite headers expires

# without the following line we get "AH00558: apache2: Could not reliably determine the server's fully qualified domain name"
# autorise .htaccess files
RUN sed -i '/<Directory \/var\/www\/>/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf

# Cleanup
RUN apt-get purge -y --auto-remove \
    && rm -rf /var/lib/apt/lists/* /tmp/* /usr/src/* \
    && apt-get clean autoclean \
    && rm -rf /var/lib/{apt,dpkg,cache,log}/
