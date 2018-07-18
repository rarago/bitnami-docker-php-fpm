FROM bitnami/minideb-extras:jessie-r22
LABEL maintainer "rarago <admin@rarago.com>"

#change source 
RUN mv /etc/apt/sources.list /etc/apt/sources.list.bak && \
    echo "deb http://mirrors.163.com/debian/ jessie main non-free contrib" >/etc/apt/sources.list && \
    echo "deb http://mirrors.163.com/debian/ jessie-proposed-updates main non-free contrib" >>/etc/apt/sources.list && \
    echo "deb-src http://mirrors.163.com/debian/ jessie main non-free contrib" >>/etc/apt/sources.list && \
    echo "deb-src http://mirrors.163.com/debian/ jessie-proposed-updates main non-free contrib" >>/etc/apt/sources.list
	
	

# Install required system packages and dependencies
RUN install_packages libbz2-1.0 libc6 libcomerr2 libcurl3 libffi6 libfreetype6 libgcc1 libgcrypt20 libgmp10 libgnutls-deb0-28 libgpg-error0 libgssapi-krb5-2 libhogweed2 libicu52 libidn11 libjpeg62-turbo libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 libldap-2.4-2 liblzma5 libmcrypt4 libncurses5 libnettle4 libp11-kit0 libpng12-0 libpq5 libreadline6 librtmp1 libsasl2-2 libssh2-1 libssl1.0.0 libstdc++6 libsybdb5 libtasn1-6 libtidy-0.99-0 libtinfo5 libxml2 libxslt1.1 zlib1g
RUN bitnami-pkg unpack php-5.6.32-0 --checksum 3f8f9865bc9cf332534869eab0a6f6a7169a29bef7e9d36801ac306678a5752c
RUN mkdir -p /bitnami && ln -sf /bitnami/php /bitnami/php-fpm

COPY rootfs /

ENV BITNAMI_APP_NAME="php-fpm" \
    BITNAMI_IMAGE_VERSION="5.6.32-r0" \
    PATH="/opt/bitnami/php/bin:/opt/bitnami/php/sbin:$PATH"
 
ENV REDIS_VERSION=3.1.1 \
	EXTENSION_DIR="/opt/bitnami/php/lib/php/extensions" \
	DEBIAN_FRONTEND=noninteractive
	

#INIT
RUN pecl channel-update pecl.php.net \
	&& pecl config-set ext_dir $EXTENSION_DIR \
	&& pear config-set ext_dir $EXTENSION_DIR
#todo fix set extension_dir of pho-config

#Install redis pecl
 
#Install redis source
RUN apt-get update \
	&& apt-get -yqq install autoconf wget build-essential \
	&& mkdir -p /tmp/php-redis \
	&& cd /tmp/php-redis \
	&& wget https://pecl.php.net/get/redis-$REDIS_VERSION.tgz \
	&& tar -xzf redis-$REDIS_VERSION.tgz --strip=1 \
	&& phpize \
	&& ./configure \
	&& make \
	&& mv modules/* $EXTENSION_DIR \
	&& mkdir -p /tmp/php-swoole \
	&& cd /tmp/php-swoole \
	&& wget http://pecl.php.net/get/swoole-2.1.0.tgz \
	&& tar -xzvf swoole-2.1.0.tgz --strip=1 \
	&& phpize \
	&& ./configure \
	&& make \
	&& mv modules/* $EXTENSION_DIR \
	&& cd / \
	&& rm -dR /tmp/php-redis \
	&& rm -dR /tmp/php-swoole \
	&& apt-get remove -yqq autoconf wget build-essential \
	&& apt-get autoremove -y \
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


ENV DEBIAN_FRONTEND teletype
 

EXPOSE 9000

WORKDIR /app
ENTRYPOINT ["/app-entrypoint.sh"]
CMD ["/run.sh"]
