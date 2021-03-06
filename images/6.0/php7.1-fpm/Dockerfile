FROM php:7.1-fpm

LABEL maintainer="mathieu.brunot at monogramm dot io"

ENV DOLI_VERSION 6.0.8

ENV DOLI_DB_TYPE mysqli
ENV DOLI_DB_HOST localhost
ENV DOLI_DB_PORT 3306
ENV DOLI_DB_USER ''
ENV DOLI_DB_PASSWORD ''
ENV DOLI_DB_NAME ''
ENV DOLI_DB_PREFIX llx_
ENV DOLI_DB_CHARACTER_SET utf8
ENV DOLI_DB_COLLATION utf8_unicode_ci

ENV DOLI_DB_ROOT_LOGIN ''
ENV DOLI_DB_ROOT_PASSWORD ''

ENV DOLI_ADMIN_LOGIN admin
ENV DOLI_MODULES ''

ENV DOLI_URL_ROOT 'http://localhost'

ENV DOLI_AUTH dolibarr
ENV DOLI_LDAP_HOST 127.0.0.1
ENV DOLI_LDAP_PORT 389
ENV DOLI_LDAP_VERSION 3
ENV DOLI_LDAP_SERVERTYPE openldap
ENV DOLI_LDAP_LOGIN_ATTRIBUTE uid
ENV DOLI_LDAP_DN ''
ENV DOLI_LDAP_FILTER ''
ENV DOLI_LDAP_ADMIN_LOGIN ''
ENV DOLI_LDAP_ADMIN_PASS ''
ENV DOLI_LDAP_DEBUG false

ENV DOLI_HTTPS 0
ENV DOLI_PROD 0
ENV DOLI_NO_CSRF_CHECK 0

ENV WWW_USER_ID 33
ENV WWW_GROUP_ID 33

ENV PHP_INI_DATE_TIMEZONE 'UTC'
ENV PHP_MEMORY_LIMIT 256M
ENV PHP_MAX_UPLOAD 20M
ENV PHP_MAX_EXECUTION_TIME 300

RUN set -ex; \
	apt-get update -q; \
	# install the packages we need
	apt-get install -y --no-install-recommends \
		rsync \
	; \
	# install the PHP extensions we need
	# see https://wiki.dolibarr.org/index.php/Dependencies_and_external_libraries
	apt-get install -y --no-install-recommends \
		libcurl4-openssl-dev \
		libfreetype6-dev \
		libjpeg-dev \
		libldap2-dev \
		libmagickcore-dev \
		libmagickwand-dev \
		libmcrypt-dev \
		libpng-dev \
		libpq-dev \
		libxml2-dev \
		unzip \
	; \
	rm -rf /var/lib/apt/lists/*; \
	debMultiarch="$(dpkg-architecture --query DEB_BUILD_MULTIARCH)"; \
	docker-php-ext-configure ldap --with-libdir="lib/$debMultiarch"; \
	docker-php-ext-configure gd --with-freetype-dir=/usr --with-png-dir=/usr --with-jpeg-dir=/usr; \
	docker-php-ext-install \
		calendar \
		gd \
		ldap \
		mbstring \
		mysqli \
		pdo \
		pdo_mysql \
		pdo_pgsql \
		pgsql \
		soap \
		zip \
	; \
    pecl install imagick; \
    docker-php-ext-enable imagick; \
    \
    mkdir -p /var/www/documents; \
    chown -R www-data:root /var/www; \
    chmod -R g=u /var/www

VOLUME /var/www/html
VOLUME /var/www/documents

# Get Dolibarr
ADD https://github.com/Dolibarr/dolibarr/archive/${DOLI_VERSION}.zip /tmp/dolibarr.zip
RUN set -ex; \
    mkdir -p /tmp/dolibarr; \
	unzip -q /tmp/dolibarr.zip -d /tmp/dolibarr; \
	rm /tmp/dolibarr.zip; \
	mkdir -p /usr/src/dolibarr; \
	cp -r /tmp/dolibarr/dolibarr-${DOLI_VERSION}/* /usr/src/dolibarr; \
	rm -rf /tmp/dolibarr; \
	chmod +x /usr/src/dolibarr/scripts/*

COPY entrypoint.sh /
RUN set -ex; \
	chmod 755 /entrypoint.sh;

ENTRYPOINT ["/entrypoint.sh"]
CMD ["php-fpm"]
