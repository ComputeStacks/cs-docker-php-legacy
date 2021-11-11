FROM ubuntu:12.04

LABEL maintainer="https://github.com/ComputeStacks"
LABEL org.opencontainers.image.authors="https://github.com/ComputeStacks"
LABEL org.opencontainers.image.source="https://github.com/ComputeStacks/cs-docker-php-legacy/tree/main/5.3-apache"
LABEL org.opencontainers.image.url="https://github.com/ComputeStacks/cs-docker-php-legacy/tree/main/5.3-apache"
LABEL org.opencontainers.image.title="PHP v5.3 with Apache"

COPY sources.list /etc/apt/sources.list

RUN set -ex; \
    \
    apt-get update; \
    apt-get install -y \
            zip \
            unzip \
            libpng-dev \
            mysql-client \
            libxml2-dev \
            libbz2-dev \
            libmcrypt-dev \
            libmemcached-dev \
            libfreetype6-dev \
            libcurl4-gnutls-dev \
            libc-client-dev \
            libkrb5-dev \
            libxslt-dev \
            vim \
            nano \
            zlib1g-dev \
            libicu-dev \
            g++ \
            wget \
            imagemagick \
            libmagickwand-dev \
            mcrypt \
            apache2 \
            php5 \
            php5-cli \
            libapache2-mod-php5 \
            php5-gd \
            php5-ldap \
            php5-mysql \
            php5-pgsql \
            php5-mcrypt \
            php5-curl \
            php5-json \
            php5-intl \
            php-pear \
    ; \
    pear install --alldeps Mail \
    ; \
    rm -rf /var/lib/apt/lists/*;

RUN a2enmod rewrite expires deflate

COPY 000-default.conf /etc/apache2/sites-enabled/000-default
COPY sample /usr/src/sample
COPY logging.conf /etc/apache2/conf.d/cslog.conf
COPY docker-entrypoint.sh /entrypoint.sh
COPY run /usr/local/bin/run
COPY ZendGuardLoader.so /usr/lib/php5/20090626/

COPY vendor/cs.ini /etc/php5/apache2/conf.d/cs.ini

RUN usermod -u 1001 www-data \
    && groupmod -g 1001 www-data \
    && chmod +x /usr/local/bin/run \
    && chmod +x /entrypoint.sh \
    ; \
    echo "zend_extension=/usr/lib/php5/20090626/ZendGuardLoader.so" > /etc/php5/conf.d/00-zend.ini \
    && echo "zend_loader.enable=1" >> /etc/php5/conf.d/00-zend.ini

WORKDIR /var/www/html

ENTRYPOINT ["/entrypoint.sh"]
EXPOSE 80
CMD ["/usr/local/bin/run"]
