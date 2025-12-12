FROM php:8.4-apache

# Change docroot
ENV APACHE_DOCUMENT_ROOT /var/www/html/
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

# Add configs
COPY ./docker/web/configs/apache/sites-enabled/* /etc/apache2/sites-enabled/

# php extensions installer: https://github.com/mlocati/docker-php-extension-installer
COPY --from=mlocati/docker-php-extension-installer /usr/bin/install-php-extensions /usr/local/bin/

# install composer
COPY --from=composer/composer:2-bin /composer /usr/bin/composer

# Install php extensions
RUN ( curl -sSLf https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions -o - || echo 'return 1' ) | sh -s \
    gd xdebug pdo_mysql mysqli dip intl

# Install ping
RUN apt-get install -y --no-install-recommends iputils-ping

# Copy custom configs into place
COPY docker/web/configs/php/*.ini $PHP_INI_DIR/conf.d/

# Enable Apache mods
RUN a2enmod ssl \
    && a2enmod allowmethods \
    && a2enmod headers \
    && a2enmod mime \
    && a2enmod rewrite

# SSL 	
RUN mkdir -p /etc/apache2/ssl
RUN openssl req -new -newkey rsa:4096 -days 3650 -nodes -x509 -subj "/C=../ST=...../L=..../O=..../CN=..." -keyout /etc/apache2/ssl/ssl.key -out /etc/apache2/ssl/ssl.crt 

# Ports exposed
EXPOSE 443 
