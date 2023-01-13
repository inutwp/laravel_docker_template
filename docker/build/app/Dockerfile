FROM php:8.1.2-fpm-alpine3.15

LABEL traefik.maintainer="inutwp <inutwp.com>"
LABEL traefik.version="v1.0"
LABEL traefik.base.image="php:8.0.13-fpm-alpine3.15"

RUN apk --update --no-cache add \
    ca-certificates \
    supervisor \
    bash \
    vim \
    tzdata \
    htop \ 
	&& cp /usr/share/zoneinfo/Asia/Jakarta /etc/localtime \
	&& echo "Asia/Jakarta" > /etc/timezone \
	&& rm -rf /var/lib/apt/lists/* \
	&& rm -rf /var/cache/apk/* \
    && apk add libpq-dev \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo pdo_pgsql pgsql \
    && rm -rf /usr/local/etc/php-fpm.d/www.conf \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && mkdir -p /var/www/inutwp

COPY docker/config/php/www.conf /usr/local/etc/php-fpm.d/www.conf

WORKDIR /var/www/inutwp

COPY --chown=www-data ./ /var/www/inutwp

RUN mkdir -p /var/www/inutwp/vendor/ && \
	chown -R www-data:www-data /var/www/inutwp \
	&& chmod -R 0644 /var/www/inutwp \
	&& find /var/www/inutwp -type d -print0 | xargs -0 chmod 0755 \
	&& chown -R www-data:www-data /run \
    && composer install -o --ignore-platform-reqs \
    && php artisan key:generate \ 
    && php artisan optimize:clear 

EXPOSE 9000

user www-data