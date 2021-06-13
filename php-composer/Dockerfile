# syntax=docker/dockerfile:1

# FROM php:7.1-apache
# FROM php:7.2-apache
# FROM php:7.3-apache
# FROM php:7.4-apache
FROM php:8.0-apache

# Install System Dependencies
RUN apt-get update \
	&& DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
	software-properties-common \
	&& apt-get update \
	&& DEBIAN_FRONTEND=noninteractive apt-get install -y \
	libfreetype6-dev \
	libicu-dev \
	libssl-dev \
	libjpeg62-turbo-dev \
	libmcrypt-dev \
	libedit-dev \
	libedit2 \
	libxslt1-dev \
	apt-utils \
	gnupg \
	redis-tools \
	default-mysql-client \
	git \
	nano \
	wget \
	curl \
	libcurl4-openssl-dev \
	lynx \
	psmisc \
	unzip \
	libzip-dev \
	zip \
	tar \
	cron \
	bash-completion \
	&& apt-get clean

# Install Magento Dependencies
# php7.4 and above
RUN docker-php-ext-configure gd --with-jpeg=/usr/include/ --with-freetype=/usr/include/

# php7.3 and below
# RUN docker-php-ext-configure gd --with-freetype-dir=/usr --with-jpeg-dir=/usr --with-png-dir=/usr

RUN docker-php-ext-install \
	bcmath \
	gd \
	intl \
	pdo_mysql \
	soap \
	xsl \
	zip \
	sockets \
	opcache

# Install oAuth
RUN apt-get update \
	&& apt-get install -y \
	libpcre3 \
	libpcre3-dev \
	# php-pear \
	&& pecl install oauth \
	&& echo "extension=oauth.so" > /usr/local/etc/php/conf.d/docker-php-ext-oauth.ini

# Install Node, NVM, NPM and Grunt
RUN curl -sL https://deb.nodesource.com/setup_6.x | bash - \
	&& curl https://raw.githubusercontent.com/creationix/nvm/v0.16.1/install.sh | sh \
	&& apt-get update && apt-get upgrade -y \
	&& apt-get install nodejs npm -y build-essential \
	&& npm i -g grunt-cli yarn

# Install Composer
RUN	curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin/ --filename=composer

# Downgrade to Composer 1
# RUN composer self-update --1

# Install Mailhog
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install golang-go \
	&& mkdir /opt/go \
	&& export GOPATH=/opt/go \
	&& go get github.com/mailhog/mhsendmail

# Install Magerun 2
RUN wget https://files.magerun.net/n98-magerun2.phar \
	&& chmod +x ./n98-magerun2.phar \
	&& mv ./n98-magerun2.phar /usr/local/bin/

# Configuring system
ADD docker/config/php.ini /usr/local/etc/php/php.ini
COPY docker/bin/* /usr/local/bin/
COPY docker/users/* /var/www/
RUN chmod +x /usr/local/bin/*

# Install Bash Completion
RUN curl -o /etc/bash_completion.d/m2install-bash-completion https://raw.githubusercontent.com/yvoronoy/m2install/master/m2install-bash-completion
RUN curl -o /etc/bash_completion.d/n98-magerun2.phar.bash https://raw.githubusercontent.com/netz98/n98-magerun2/master/res/autocompletion/bash/n98-magerun2.phar.bash
RUN echo "source /etc/bash_completion" >> /root/.bashrc
RUN echo "source /etc/bash_completion" >> /var/www/.bashrc

RUN chmod 777 -Rf /var/www /var/www/.* \
	&& chown -Rf www-data:www-data /var/www /var/www/.* \
	&& usermod -u 1000 www-data \
	&& chsh -s /bin/bash www-data\
	&& a2enmod rewrite \
	&& a2enmod headers

VOLUME /var/www/html
WORKDIR /var/www/html

CMD apachectl -D FOREGROUND