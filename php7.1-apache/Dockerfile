FROM php:7.1-apache
ADD ./docker-php.conf /etc/apache2/conf-enabled/docker-php.conf #添加 rewrite_module 
#php.ini-development || php.ini-production
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini" #使用正式环境配置文件
RUN apt-get update && apt-get install -y \
        cron \ #定时任务
        libfreetype6-dev \ #gd
        libjpeg62-turbo-dev \ #gd
        libpng-dev \ #gd
        libxml2 \ #soap
        libxml2-dev \ #soap
    && docker-php-ext-install -j$(nproc) iconv \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \ #安装gd
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
    && docker-php-ext-enable soap #安装soap
#更改项目根目录为 /var/www/html/public
ENV APACHE_DOCUMENT_ROOT /var/www/html/public
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf
#添加定时任务
RUN echo "* * * * * root php /var/www/html/artisan schedule:run >> /dev/null 2>&1" >> /etc/crontab && cron start
