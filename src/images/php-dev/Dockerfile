ARG CO_VERSION
ARG PHP_VERSION
FROM gpupo/container-orchestration:php-fpm-${CO_VERSION}

# Labels.
LABEL org.label-schema.name="gpupo/container-orchestration:php-dev"
LABEL org.label-schema.description="Image with PHP ${PHP_VERSION} FPM and Debian and Development tools"
LABEL org.label-schema.docker.cmd="docker run -v "$PWD":/var/www/app gpupo/container-orchestration:php-dev"


RUN  pecl install xdebug-2.6.0 \
    && docker-php-ext-enable xdebug; \
    apt-get install -y libxslt-dev; docker-php-ext-install xsl;\
    docker-php-ext-install xmlrpc; pecl install ast && docker-php-ext-enable ast;\
    composer global config minimum-stability dev;\
    composer global config prefer-stable true;\
    composer global require symfony/dotenv gpupo/common symfony/var-dumper symfony/finder phpspec/prophecy \
    phpunit/phpunit:^7.3 friendsofphp/php-cs-fixer:^2.14 "squizlabs/php_codesniffer=*" escapestudios/symfony2-coding-standard:3.x-dev\
    phploc/phploc pdepend/pdepend phpmd/phpmd sebastian/phpcpd phpstan/phpstan maknz/slack phan/phan liip/rmt; \
    /root/.composer/vendor/bin/phpcbf --config-set installed_paths /root/.composer/vendor/escapestudios/symfony2-coding-standard;

RUN cd /root/; git clone https://github.com/adsr/phpspy.git; cd phpspy; make; mv phpspy /usr/bin/; cd ..; rm -rf phpspy;
