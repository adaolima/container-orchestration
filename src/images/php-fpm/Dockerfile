ARG PHP_VERSION
ARG CO_VERSION
ARG BUILD_DATE
ARG VCS_REF
ARG BUILD_VERSION

FROM php:${PHP_VERSION}-fpm
# see https://hub.docker.com/_/php/

# Labels.
LABEL org.label-schema.schema-version="1.0"
LABEL org.label-schema.build-date=$BUILD_DATE
LABEL org.label-schema.name="gpupo/container-orchestration:php-fpm"
LABEL org.label-schema.description="Image with PHP ${PHP_VERSION} FPM and Debian"
LABEL org.label-schema.url="https://opensource.gpupo.com/container-orchestration/"
LABEL org.label-schema.vcs-url="https://github.com/gpupo/container-orchestration"
LABEL org.label-schema.vcs-ref=$VCS_REF
LABEL org.label-schema.vendor="gpupo"
LABEL org.label-schema.version=$BUILD_VERSION
LABEL org.label-schema.docker.cmd="docker run -v "$PWD":/var/www/app gpupo/container-orchestration:php-fpm"


RUN set -ex; apt-get -q update; apt-get install -y --no-install-recommends apt-utils iputils-ping procps;\
    apt-get -q install -y git unzip zlib1g-dev libpng-dev libjpeg-dev gettext-base libxml2-dev libzip-dev; \
    apt-get -q install -y curl libmcrypt-dev mysql-client libicu-dev; \
    apt-get -q install -y libcurl4-openssl-dev pkg-config libssl-dev telnet vim netcat; \
    docker-php-ext-configure intl; \
    docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr; \
    docker-php-ext-install calendar intl soap zip pdo pdo_mysql mbstring bcmath sockets exif fileinfo;\
    pecl install apcu && docker-php-ext-enable apcu;\
    pecl install mongodb && docker-php-ext-enable mongodb;

RUN pecl install redis && docker-php-ext-enable redis;

RUN docker-php-ext-install gd  mysqli opcache ctype json mbstring; \
  usermod --shell /bin/bash www-data; \
  usermod --home /var/www/app www-data; \
  useradd -u 1001 -M docker; \
  usermod -a -G docker www-data; \
  usermod -a -G www-data docker; \
  useradd -u 1000 -M rkt; \
  usermod -a -G rkt www-data; \
  usermod -a -G www-data rkt;

COPY php.ini /usr/local/etc/php/
COPY opcache-recommended.ini /usr/local/etc/php/conf.d/opcache-recommended.ini

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"; \
    php composer-setup.php; \
    php -r "unlink('composer-setup.php');"

RUN mkdir -p /usr/local/bin/;\
    mv -v composer.phar /usr/local/bin/composer; chmod +x /usr/local/bin/composer; \
    composer global require bamarni/symfony-console-autocomplete:dev-master;

COPY .bashrc /root/

WORKDIR /var/www/app

EXPOSE 9000
