FROM ubuntu:16.04

ENV WORDPRESS_VERSION 4.9.13

# Keep upstart from complaining
RUN dpkg-divert --local --rename --add /sbin/initctl
RUN ln -sf /bin/true /sbin/initctl

# Let the conatiner know that there is no tty
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update
RUN apt-get -y upgrade
 
# Requirements
RUN apt-get install -y \
    apache2 \
    cron \
    curl \
    gawk \
    git \
    imagemagick \
    libapache2-mod-php \
    libapache2-mod-webauthldap \
    libcurl3 \
    libcurl3-dev \
    php \
    php-curl \
    php-gd \
    php-intl \
    php-imagick \
    php-ldap \
    php-mbstring \
    php-mysql \
    php-pgsql \
    php-soap \
    php-xml \
    php-zip \
    rsync \
    supervisor \
    unzip \
    vim \
    wget

# WordPress herunter laden
RUN mkdir -p /opt/wordpress
RUN wget https://wordpress.org/wordpress-${WORDPRESS_VERSION}.tar.gz --directory-prefix /tmp
RUN tar zxvf /tmp/wordpress-${WORDPRESS_VERSION}.tar.gz -C /opt/wordpress

# WordPress CLI installieren
RUN mkdir -p /opt/wp-cli
RUN wget https://github.com/wp-cli/wp-cli/releases/latest -O /opt/wp-cli/latest
RUN wget $(awk -F '[<>]' '/href=".*phar"/ {match($0,"href=\"(.*.phar)\"",a); print "https://github.com/" a[1]}' /opt/wp-cli/latest) -O wp-cli.phar
RUN chmod ugo+x wp-cli.phar
RUN chown root:root wp-cli.phar
RUN mv wp-cli.phar /usr/local/bin/wp

COPY ./apache.sh /opt/apache.sh
RUN chmod +x /opt/apache.sh

COPY ./conf/supervisord.conf /etc/supervisord.conf

# SSL
RUN mkdir -p /etc/apache2/ssl

RUN rm /etc/apache2/sites-enabled/*
ADD ./conf/default-ssl.conf /etc/apache2/sites-enabled
ADD ./conf/default.conf /etc/apache2/sites-enabled

ADD ./conf/remoteip.conf /etc/apache2/conf-available

# Crontab
RUN echo "5 * * * * www-data /usr/bin/php /var/www/html/wp-cron.php" >> /etc/crontab

RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

EXPOSE 80 443
