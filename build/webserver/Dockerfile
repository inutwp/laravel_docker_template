FROM nginx:1.22-alpine

LABEL webserver.maintainer="inutwp <inutwp.com>"
LABEL webserver.version="v1.0"
LABEL webserver.base.image="nginx:1.22-alpine"

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
	&& rm -rf /var/cache/apk/*

COPY /src/ /var/www/inutwp

RUN rm -rf /etc/nginx/nginx.conf \
    && rm -rf /etc/nginx/conf.d/default.conf \
    && mkdir -p /etc/nginx/sites-available/ \
    && mkdir -p /etc/nginx/sites-enabled/ \
    && ln -s /etc/nginx/sites-available/inutwp.conf /etc/nginx/sites-enabled/inutwp.conf

COPY /config/nginx/nginx.conf /etc/nginx/nginx.conf
COPY /config/nginx/conf.d/inutwp.conf /etc/nginx/conf.d/inutwp.conf
COPY /config/nginx/conf.d/inutwp.conf /etc/nginx/sites-available/inutwp.conf

WORKDIR /var/www/inutwp