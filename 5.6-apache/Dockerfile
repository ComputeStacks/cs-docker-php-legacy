FROM php:5.6-apache-stretch

LABEL maintainer="https://computestacks.com"
LABEL org.opencontainers.image.authors="https://computestacks.com"
LABEL org.opencontainers.image.source="https://github.com/ComputeStacks/cs-docker-php-legacy/tree/main/5.6-apache"
LABEL org.opencontainers.image.url="https://github.com/ComputeStacks/cs-docker-php-legacy/tree/main/5.6-apache"
LABEL org.opencontainers.image.title="PHP v5.6 with Apache"

RUN set -ex; \
    \
    apt-get update; \
    apt-get install -y \
            zip \
            unzip \
            libpng-dev \
            libjpeg62-turbo-dev \
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
    ; \
    rm -rf /var/lib/apt/lists/*;

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install imap \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && pecl install imagick-3.4.3 \
    && docker-php-ext-enable imagick \
    && docker-php-ext-install -j$(nproc) \
                                pdo_mysql \
                                mysqli \
                                soap \
                                mbstring \
                                opcache \
                                bcmath \
                                bz2 \
                                curl \
                                iconv \
                                ftp \
                                soap \
                                json \
                                posix \
                                session \
                                sockets \
                                xml \
                                xmlrpc \
                                xmlwriter \
                                xsl \
                                zip \
                                wddx \
                                mcrypt \
                                mysql \
    ; \
    { \
        echo 'opcache.memory_consumption=128'; \
        echo 'opcache.interned_strings_buffer=8'; \
        echo 'opcache.max_accelerated_files=4000'; \
        echo 'opcache.revalidate_freq=2'; \
        echo 'opcache.fast_shutdown=1'; \
        echo 'opcache.enable_cli=1'; \
    } > /usr/local/etc/php/conf.d/opcache-recommended.ini \
    ; \
    a2enmod rewrite expires deflate \
    ; \
    pear channel-update pear.php.net \
    && pear install --alldeps Mail

RUN curl -L -o /tmp/redis.tar.gz https://github.com/phpredis/phpredis/archive/3.1.3.tar.gz \
    && tar -xzvf /tmp/redis.tar.gz -C /tmp \
    && mkdir -p /usr/src/php/ext \
    && mv /tmp/phpredis-3.1.3 /usr/src/php/ext/redis \
    && rm -rf /tmp/redis.tar.gz \
    && docker-php-ext-install redis

RUN curl -L -o /tmp/memcached.tar.gz http://pecl.php.net/get/memcached-2.2.0.tgz \
    && tar -xzvf /tmp/memcached.tar.gz \
    && mv memcached-2.2.0 /usr/src/php/ext/memcached \
    && rm /tmp/memcached.tar.gz \
    && docker-php-ext-install memcached

COPY 000-default.conf /etc/apache2/sites-enabled
COPY sample /usr/src/sample
COPY logging.conf /etc/apache2/conf-enabled
COPY docker-entrypoint.sh /entrypoint.sh

COPY vendor/ioncube_loader_lin_5.6.so /usr/local/lib/php/extensions/no-debug-non-zts-20131226
COPY vendor/zend-guard/opcache.so /usr/local/lib/php/extensions/no-debug-non-zts-20131226
COPY vendor/zend-guard/ZendGuardLoader.so /usr/local/lib/php/extensions/no-debug-non-zts-20131226
COPY vendor/00-ioncube.ini /usr/local/etc/php/conf.d
COPY vendor/cs.ini /usr/local/etc/php/conf.d
COPY vendor/zend-guard/00-zend.ini /usr/local/etc/php/conf.d


RUN chown root:staff /usr/local/lib/php/extensions/no-debug-non-zts-20131226/ioncube_loader_lin_5.6.so \
    && chmod +x /usr/local/lib/php/extensions/no-debug-non-zts-20131226/ioncube_loader_lin_5.6.so \
    && chmod +x /usr/local/lib/php/extensions/no-debug-non-zts-20131226/ZendGuardLoader.so \
    && chmod +x /usr/local/lib/php/extensions/no-debug-non-zts-20131226/opcache.so

RUN rm /var/www/html/package.xml

RUN usermod -u 1001 www-data \
    && groupmod -g 1001 www-data

WORKDIR /var/www/html

ENTRYPOINT ["/entrypoint.sh"]

CMD ["apache2-foreground"]
