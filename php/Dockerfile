FROM php:fpm

RUN apt-get update 

RUN apt-get install -y \
    libssl-dev pkg-config

RUN pecl install mongodb \
	&& docker-php-ext-enable mongodb

RUN apt-get install libmcrypt-dev -y \
	&& docker-php-ext-install -j$(nproc) mcrypt

RUN apt-get install unzip -y \
	&& curl -fsSLO 'https://github.com/tideways/php-profiler-extension/archive/master.zip' \
	&& mkdir -p /tmp/tideways \
    && unzip master.zip -d /tmp/tideways \
    && rm master.zip \
    && docker-php-ext-configure /tmp/tideways/php-profiler-extension-master \
    && docker-php-ext-install /tmp/tideways/php-profiler-extension-master \
    && rm -rf /tmp/tideways \
    && echo tideways.auto_prepend_library=0 >>/usr/local/etc/php/conf.d/docker-php-ext-tideways.ini

RUN docker-php-ext-install -j$(nproc) pdo_mysql

RUN apt-get install git -y

RUN mkdir /code && chown -R www-data /code

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
	&& php -r "if (hash_file('SHA384', 'composer-setup.php') === '669656bab3166a7aff8a7506b8cb2d1c292f042046c5a994c43155c0be6190fa0355160742ab2e1c88d40d5be660b410') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
	&& php composer-setup.php \
	&& php -r "unlink('composer-setup.php');" \
	&& mv composer.phar /usr/local/bin/composer

WORKDIR /code

CMD ["php-fpm"]