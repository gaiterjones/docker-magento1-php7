FROM gaiterjones/magento1:1.9.4.0
LABEL maintainer "paj@gaiterjones.com"
LABEL description "Magento 1.9"
# ------------------------------------------------------------------

# configs
#
COPY ./php/php.ini /usr/local/etc/php/conf.d/php.ini
COPY ./apache/apache2.conf /etc/apache2/apache2.conf
COPY ./apache/000-default.conf /etc/apache2/sites-available/000-default.conf
COPY ./apache/remoteip.conf /etc/apache2/conf-available/remoteip.conf
COPY ./php/msmtprc /etc/msmtprc

# msmtprc smtp config
#
ARG APPDOMAIN
ARG SMTP
RUN set -x \
	&& sed -i "s/XMAILHOSTX/$SMTP/g" /etc/msmtprc \
	&& sed -i "s/XMAILDOMAINX/$APPDOMAIN/g" /etc/msmtprc \
	&& cat /etc/msmtprc

# enable mods and sites
#
RUN a2enmod rewrite \
	&& a2enmod expires \
	&& a2enmod headers \
	&& a2enmod remoteip \
	&& a2enconf remoteip \
	&& a2ensite 000-default.conf

# configure apache env
#
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid
ENV APACHE_RUN_DIR /var/run/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2

# cleanup
#
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

WORKDIR /var/www/dev/magento

# cron job
#
ADD crontab /etc/cron.d/magento-cron
RUN chmod 0644 /etc/cron.d/magento-cron
RUN crontab -u www-data /etc/cron.d/magento-cron
