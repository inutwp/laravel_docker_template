FROM nginx:1.23.3-alpine-slim

LABEL webserver.maintainer="inutwp <inutwp.com>"
LABEL webserver.version="v1.0"
LABEL webserver.base.image="nginx:1.23.2-alpine-slime"

RUN apk --update --no-cache add \
    supervisor \
    bash \
    tzdata \
	&& cp /usr/share/zoneinfo/Asia/Jakarta /etc/localtime \
	&& echo "Asia/Jakarta" > /etc/timezone \
	&& rm -rf /var/lib/apt/lists/* \
	&& rm -rf /var/cache/apk/*

RUN rm -rf /etc/nginx/nginx.conf \
    && rm -rf /etc/nginx/conf.d/default.conf \
    && mkdir -p /etc/nginx/sites-available/ \
    && mkdir -p /etc/nginx/sites-enabled/ \
    && ln -s /etc/nginx/sites-available/inutwp.conf /etc/nginx/sites-enabled/inutwp.conf

COPY docker/config/nginx/nginx.conf /etc/nginx/nginx.conf
COPY docker/config/nginx/conf.d/inutwp.conf /etc/nginx/conf.d/inutwp.conf
COPY docker/config/nginx/conf.d/inutwp.conf /etc/nginx/sites-available/inutwp.conf
COPY docker/config/supervisord/supervisord_webserver.conf /etc/supervisord.conf

WORKDIR /var/www/inutwp

COPY --chown=nginx:nginx ./ /var/www/inutwp

RUN chmod -R 0644 /var/www/inutwp \
	&& find /var/www/inutwp -type d -print0 | xargs -0 chmod 0755 \
    && chown -R nginx:0 /var/log/nginx \
	&& chown -R nginx:nginx /run \
    && chown -R nginx:0 /var/cache/nginx \
    && chmod -R g+w /var/cache/nginx \
    && chown -R nginx:0 /etc/nginx \
    && chmod -R g+w /etc/nginx

USER nginx

CMD ["/usr/bin/supervisord", "-n", "-c" ,"/etc/supervisord.conf"]