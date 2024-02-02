FROM alpine:3.17

LABEL maintainer="Ricardo Brito do Nascimento <ricardo.brito@presidencia.gov.br>"

WORKDIR /var/www/html

ARG HTTP_PROXY=""
ARG HTTPS_PROXY=""
ARG OJS_VERSION=3_3_0-15

# When using Composer, disable the warning about running commands as root/super user
ENV COMPOSER_ALLOW_SUPERUSER=1  \
    http_proxy=${HTTP_PROXY}  \
    https_proxy=${HTTPS_PROXY}  \
    HTTP_PROXY=${HTTP_PROXY}  \
    HTTPS_PROXY=${HTTPS_PROXY}  \
	SERVERNAME="localhost"      \
	HTTPS="on"                  \
	OJS_VERSION=${OJS_VERSION} \
	OJS_CLI_INSTALL="0"         \
	OJS_DB_HOST="localhost"     \
	OJS_DB_USER="ojs"           \
	OJS_DB_PASSWORD="ojs"       \
	OJS_DB_NAME="ojs"           \
	OJS_WEB_CONF="/etc/apache2/conf.d/ojs.conf"	\
	OJS_CONF="/var/www/html/config.inc.php"


# PHP_INI_DIR to be symmetrical with official php docker image
ENV PHP_INI_DIR /etc/php/8.1

# Basic packages
ENV PACKAGES 		\
	apache2 		\
	apache2-ssl 	\
	apache2-utils 	\
	ca-certificates \
	curl 			\
	ttf-freefont	\
	dcron 			\
	patch			\
	php81			\
	php81-apache2	\
	runit

# PHP extensions
ENV PHP_EXTENSIONS	\
    gnu-libiconv    \
    php81-bcmath		\
    php81-bz2		\
    php81-calendar	\
    php81-ctype		\
    php81-curl		\
    php81-dom		\
    php81-exif		\
    php81-fileinfo	\
    php81-ftp		\
    php81-gettext	\
    php81-iconv		\
    php81-intl		\
    php81-json		\
    php81-mbstring	\
    php81-mysqli		\
    php81-opcache	\
    php81-openssl	\
    php81-pdo_mysql	\
    php81-phar		\
    php81-posix		\
    php81-session	\
    php81-shmop		\
    php81-simplexml	\
    php81-sockets	\
    php81-sysvmsg	\
    php81-sysvsem	\
    php81-sysvshm	\
    php81-tokenizer	\
    php81-xml		\
    php81-xmlreader	\
    php81-xmlwriter	\
    php81-zip		\
    php81-pdo_pgsql  \
    php81-pgsql      \
    php81-zlib

# Required to build OJS:
ENV BUILDERS 		\
	git 			\
	nodejs 			\
	npm

# To make a smaller image, we start with the copy.
# This let us joining runs in a single layer.
COPY exclude.list /tmp/exclude.list

RUN set -xe \
	&& apk add --no-cache --virtual .build-deps $BUILDERS \
	&& apk add --no-cache $PACKAGES \
	&& apk add --no-cache $PHP_EXTENSIONS \
	# && apk add --no-cache --repository http://dl-cdn.alpinelinux.org/alpine/v3.12/community/ gnu-libiconv=1.15-r2 \
	# && apk add --no-cache --repository https://dl-cdn.alpinelinux.org/alpine/v3.16/main/ nodejs=16.20.2-r0 \
# Building OJS:
	# Configure and download code from git
	&& git config --global url.https://.insteadOf git:// \
	&& git config --global advice.detachedHead false \
	&& git clone --depth 1 --single-branch --branch $OJS_VERSION --progress https://github.com/pkp/ojs.git . \
	&& git submodule update --init --recursive >/dev/null \
	# Composer vudu:
 	&& curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer.phar \
	# To avoid timeouts with gitHub we use tokens:
	# TODO: Replace personal token by an official one.
 	# && composer.phar config -g github-oauth.github.com 58778f1c172c09f3add6cb559cbadd55de967d47 \
	# Install Composer Deps:
 	&& composer.phar --working-dir=lib/pkp install --no-dev \
 	&& composer.phar --working-dir=plugins/paymethod/paypal install --no-dev \
	&& composer.phar --working-dir=plugins/generic/citationStyleLanguage install --no-dev \
	# Node joins to the party:
    && npm config set proxy ${HTTPS_PROXY}  \
    && rm -rf package-lock.json  \
    && npm install -y \
    # && npm run build \
    # && npm ci -y   \
    && NODE_OPTIONS=--openssl-legacy-provider npm run build  \
# Create directories
 	&& mkdir -p /var/www/files /run/apache2  \
	&& cp config.TEMPLATE.inc.php config.inc.php \
	&& chown -R apache:apache /var/www/* \
# Prepare freefont for captcha
	&& ln -s /usr/share/fonts/TTF/FreeSerif.ttf /usr/share/fonts/FreeSerif.ttf \
# Prepare crontab
	&& echo "0 * * * *   ojs-run-scheduled" | crontab - \
# Prepare httpd.conf
	&& sed -i -e '\#<Directory />#,\#</Directory>#d' /etc/apache2/httpd.conf \
	&& sed -i -e "s/^ServerSignature.*/ServerSignature Off/" /etc/apache2/httpd.conf \
# Clear the image (list of files to be deleted in exclude.list).
	&& cd /var/www/html \
 	&& rm -rf $(cat /tmp/exclude.list) \
	&& apk del --no-cache .build-deps \
	&& rm -rf /tmp/* \
	&& rm -rf /root/.cache/* \
# Some folders are not required (as .git .travis.yml test .gitignore .gitmodules ...)
	&& find . -name ".git" -exec rm -Rf '{}' \; \
	&& find . -name ".travis.yml" -exec rm -Rf '{}' \; \
	&& find . -name "test" -exec rm -Rf '{}' \; \
	&& find . \( -name .gitignore -o -name .gitmodules -o -name .keepme \) -exec rm -Rf '{}' \;

COPY root/ /

EXPOSE 80 443

VOLUME [ "/var/www/files", "/var/www/html/public" ]

RUN chmod +x /usr/local/bin/ojs-start
CMD ["ojs-start"]
