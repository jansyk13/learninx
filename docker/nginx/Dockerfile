FROM debian:jessie

MAINTAINER NGINX Docker Maintainers "docker-maint@nginx.com"

ENV NGINX_VERSION 1.11.10-1~jessie

RUN apt-key adv --keyserver hkp://pgp.mit.edu:80 --recv-keys 573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62 \
	&& echo "deb http://nginx.org/packages/mainline/debian/ jessie nginx" >> /etc/apt/sources.list \
	&& apt-get update \
	&& apt-get install --no-install-recommends --no-install-suggests -y \
						ca-certificates \
						nginx=${NGINX_VERSION} \
						nginx-module-xslt \
						nginx-module-geoip \
						nginx-module-image-filter \
						nginx-module-perl \
						nginx-module-njs \
						gettext-base \
	&& rm -rf /var/lib/apt/lists/*

RUN apt-get update
RUN apt-get install -y git curl g++ make build-essential python cmake pkg-config

RUN export PKG_CONFIG_PATH=/usr/lib:/usr/local/lib

RUN git clone https://github.com/edenhill/kafkacat.git
RUN cd /kafkacat; ./bootstrap.sh

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log

RUN find -iname *kafkacat*

CMD tail -F /var/log/nginx/access.log | kafkacat/kafkacat -D \n -b $BROKER -t nginx -z snappy

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]
