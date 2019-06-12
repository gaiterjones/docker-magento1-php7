FROM gaiterjones/phusion1100-apache2-php7-2
LABEL maintainer "paj@gaiterjones.com"
LABEL description "Magento 1 PHP7.2-APACHE Service"
# ------------------------------------------------------------------

# MAGENTO1 dependencies
#
RUN requirements="libpng-dev libmcrypt-dev libmcrypt4 libcurl3-dev libfreetype6 libjpeg-turbo8 libjpeg-turbo8-dev libfreetype6-dev libicu-dev libxslt1-dev msmtp nano git mysql-client wget" \
    && apt-get update && apt-get install -y $requirements && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && docker-php-ext-install bcmath \
    && docker-php-ext-install mbstring \
    && docker-php-ext-install zip \
    && docker-php-ext-install intl \
    && docker-php-ext-install xsl \
    && docker-php-ext-install soap \
    && docker-php-ext-install opcache \
    && pecl install mcrypt-1.0.1 \
    && requirementsToRemove="libpng-dev libmcrypt-dev libcurl3-dev libfreetype6-dev libjpeg-turbo8-dev" \
    && apt-get purge --auto-remove -y $requirementsToRemove

# Install REDIS extension
#
RUN curl -L -o /tmp/redis.tar.gz https://github.com/phpredis/phpredis/archive/4.2.0.tar.gz \
    && tar xfz /tmp/redis.tar.gz \
    && rm -r /tmp/redis.tar.gz \
    && mv phpredis-4.2.0 /usr/src/php/ext/redis \
    && docker-php-ext-install redis

# Install memcache extension
#
RUN set -x \
    && apt-get update && apt-get install -y --no-install-recommends unzip libssl-dev libpcre3 libpcre3-dev \
    && cd /tmp \
    && curl -sSL -o php7.zip https://github.com/websupport-sk/pecl-memcache/archive/php7.zip \
    && unzip php7 \
    && cd pecl-memcache-php7 \
    && /usr/local/bin/phpize \
    && ./configure --with-php-config=/usr/local/bin/php-config \
    && make \
    && make install \
    && echo "extension=memcache.so" > /usr/local/etc/php/conf.d/ext-memcache.ini \
    && rm -rf /tmp/pecl-memcache-php7 php7.zip

# MAGENTO 1 source
#
ENV MAGENTO_VERSION 1.9.4.0

RUN mkdir /var/www/dev \
    && cd /tmp \
    && curl https://pe.terjon.es/dropbox/magento-$MAGENTO_VERSION.tar.gz -o $MAGENTO_VERSION.tar.gz && tar xvf $MAGENTO_VERSION.tar.gz \
    && mv magento /var/www/dev/ \
    && chown -R www-data:www-data /var/www/dev/magento

# scripts / sample data
#
COPY ./bin/install-magento /usr/local/bin/install-magento
RUN chmod +x /usr/local/bin/install-magento
COPY ./sampledata/magento-sample-data-1.9.2.4-2016-10-11-07-38-13.tar.gz /opt/
COPY ./bin/install-sampledata-1.9 /usr/local/bin/install-sampledata
RUN chmod +x /usr/local/bin/install-sampledata

# n98-magerun / MODMAN
#
RUN curl http://files.magerun.net/n98-magerun-latest.phar -o /n98-magerun.phar & \
    curl https://raw.githubusercontent.com/colinmollenhour/modman/master/modman -o /modman & \
    wait
RUN chmod +x /n98-magerun.phar \
	&& chmod +x /modman \
	&& mv /modman /usr/local/bin \
	&& mv /n98-magerun.phar /usr/local/bin

# cleanup
#
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

WORKDIR /var/www/dev/magento
