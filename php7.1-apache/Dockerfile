FROM php:7.1-apache
RUN sed '1i\LoadModule rewrite_module /usr/lib/apache2/modules/mod_rewrite.so' /etc/apache2/conf-enabled/docker-php.conf
#php.ini-development || php.ini-production
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"
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
        && cd ../pdo \
        && phpize \
        && ./configure --enable-pdo \
        && make -j "$(nproc)" \
        && make install \
        && cd ../pdo_mysql \
        && phpize \
        && ./configure --enable-pdo_mysql \
        && make -j "$(nproc)" \
        && make install \
    ) \
    && rm -r php-7.1.30 \
    && pecl install redis-5.0.0 \
    && docker-php-ext-enable soap pdo pdo_mysql redis
#更改项目根目录为 /var/www/html/public
ENV APACHE_DOCUMENT_ROOT /var/www/html/public
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf
#添加定时任务
RUN echo "* * * * * root php /var/www/html/artisan schedule:run >> /dev/null 2>&1" >> /etc/crontab && cron start
