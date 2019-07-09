FROM php:7.1-apache
ADD ./docker-php.conf /etc/apache2/conf-enabled/docker-php.conf
RUN apt-get update && apt-get install -y \
        cron \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libxml2 \
        libxml2-dev \
    && docker-php-ext-install -j$(nproc) iconv \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && curl -fsSL 'https://www.php.net/distributions/php-7.1.30.tar.gz' -o php-7.1.30.tar.gz \
    && mkdir -p php-7.1.30 \
    && tar -xf php-7.1.30.tar.gz -C php-7.1.30 --strip-components=1 \
    && rm php-7.1.30.tar.gz \
    && ( \
        cd php-7.1.30/ext/soap \
        && phpize \
        && ./configure --enable-soap \
        && make -j "$(nproc)" \
        && make install \
    ) \
    && rm -r php-7.1.30 \
    && docker-php-ext-enable soap

ENV APACHE_DOCUMENT_ROOT /var/www/html/public

RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf
