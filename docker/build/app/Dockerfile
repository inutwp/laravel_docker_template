FROM php:8.1.2-fpm-alpine3.15

LABEL traefik.maintainer="inutwp <inutwp.com>"
LABEL traefik.version="v1.0"
LABEL traefik.base.image="php:8.0.13-fpm-alpine3.15"

RUN apk --update --no-cache add \
    supervisor \
    bash \
    tzdata \
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
COPY docker/config/supervisord/supervisord_app.conf /etc/supervisord.conf

WORKDIR /var/www/inutwp

COPY --chown=www-data:www-data ./ /var/www/inutwp

RUN mkdir -p /var/www/inutwp/vendor/ \
	&& chmod -R 0644 /var/www/inutwp \
	&& find /var/www/inutwp -type d -print0 | xargs -0 chmod 0755 \
	&& chown -R www-data:www-data /run \
    && cp .env.example .env \
    && composer install -o --apcu-autoloader --ignore-platform-reqs \
    && sed -i -e "s/APP_NAME=Laravel/APP_NAME=inutwp/g" .env \
    && sed -i -e "s/APP_ENV=local/APP_ENV=production/g" .env \
    && sed -i -e "s/APP_DEBUG=true/APP_DEBUG=false/g" .env \
    && sed -i -e "s#APP_URL=http://localhost#APP_URL=https://inutwp.test#g" .env \
    && sed -i -e "s#APP_TIMEZONE=UTC#APP_TIMEZONE=Asia/Jakarta#g" .env \
    && php artisan key:generate \  
    && php artisan storage:link \
    && php artisan optimize:clear 

EXPOSE 9000

USER www-data

CMD ["/usr/bin/supervisord", "-n", "-c" ,"/etc/supervisord.conf"]