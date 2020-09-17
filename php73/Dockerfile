FROM php:7.3-cli

LABEL version="1.0" 
LABEL maintainer="Benjamin VILLATTE <bvillatte@gmail.com>"
LABEL description="Launch symfony cli server with PHP7.3 CLI"

ARG USER_ID
ARG GROUP_ID

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        g++ \
        wget \
        locales \
        apt-utils \
        git \
        curl \
        libmemcached-dev \
        libz-dev \
        libpq-dev \
        libjpeg-dev \
        libpng-dev \
        libfreetype6-dev \
        libssl-dev \
        libmcrypt-dev \
        libicu-dev \
        libzip-dev \
        sqlite3 \
        libsqlite3-dev \
        zip \
        unzip \
        libmagickwand-dev

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    echo "fr_FR.UTF-8 UTF-8" >> /etc/locale.gen && \
    locale-gen

RUN docker-php-ext-configure intl
RUN docker-php-ext-install pdo_mysql gd opcache intl zip calendar exif
RUN pecl install apcu && docker-php-ext-enable apcu

RUN docker-php-ext-configure gd \
        --with-jpeg-dir=/usr/lib \
        --with-freetype-dir=/usr/include/freetype2

RUN curl -sSk https://getcomposer.org/installer | php -- --disable-tls && \
   mv composer.phar /usr/local/bin/composer

# RUN curl -sL https://deb.nodesource.com/setup_10.x | bash -
# RUN curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
# RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
# RUN apt-get update && apt-get install yarn -y

RUN pecl install -o -f xdebug
RUN export XDEBUG_PATH=`find / -name "xdebug.so"` && \
    echo "zend_extension=$XDEBUG_PATH" > /usr/local/etc/php/conf.d/ext-xdebug.ini && \
    echo "xdebug.remote_host="`hostname -i` >> /usr/local/etc/php/conf.d/ext-xdebug.ini

RUN wget https://get.symfony.com/cli/installer -O - | bash && \
  mv /root/.symfony/bin/symfony /usr/local/bin/symfony

EXPOSE 8000

RUN groupadd -f --gid $GROUP_ID user
RUN adduser --disabled-password --gecos '' --uid $USER_ID --gid $GROUP_ID user
USER user

WORKDIR /usr/src/app

ENTRYPOINT symfony serve --allow-http --no-tls --port=8000
