FROM 	alpine
MAINTAINER test_for_check

ENV	TIME_ZONE "Europe/Moscow"

COPY 	initialized_db.sh /sbin/initialized_db.sh
COPY	docker-entrypoint.sh /sbin/docker-entrypoint.sh
COPY	apache.conf /config/apache.conf
COPY	davical.php /config/davical.php
COPY	rsyslog.conf /config/rsyslog.conf

RUN	apk --update add \
	sudo \
	bash \
	less \
	sed \
	supervisor \
	rsyslog \
	postgresql \
	apache2 \
	apache2-utils \
	apache2-ssl \
	php7 \
	php7-apache2 \
	php7-pgsql \
	php7-imap \
	php7-curl \
	php7-cgi \
	php7-xml \
	php7-gettext \ 
	php7-iconv \ 
	php7-ldap \
	php7-pdo \
	php7-pdo_pgsql \
	php7-calendar \
	perl \
	perl-yaml \
	perl-dbd-pg \
	perl-dbi \
	git \
# git
	&& git clone https://gitlab.com/davical-project/awl.git /usr/share/awl/ \
	&& git clone https://gitlab.com/davical-project/davical.git /usr/share/davical/ \
	&& rm -rf /usr/share/davical/.git /usr/share/awl/.git/ \
	&& apk del git \

RUN 	cd /tmp \
	&& git clone https://gitlab.com/davical-project/awl.git \
	&& git clone https://gitlab.com/davical-project/davical.git \
	&& apk del git \
  	&& rm -rf /var/cache/apk/* \
	&& rm -rf ./awl/.git/ \
	&& rm -rf ./davical/.git/ \
	&& mv davical/ /usr/share/ \
	&& mv awl/ /usr/share/ \
	&& chown -R root:apache /usr/share/davical \
	&& cd /usr/share/davical/ \
	&& find ./ -type d -exec chmod u=rwx,g=rx,o=rx '{}' \; \
	&& find ./ -type f -exec chmod u=rw,g=r,o=r '{}' \; \
	&& find ./ -type f -name *.sh -exec chmod u=rwx,g=r,o=rx '{}' \; \
	&& find ./ -type f -name *.php -exec chmod u=rwx,g=rx,o=r '{}' \; \
	&& chmod o=rx /usr/share/davical/dba/update-davical-database \
	&& chmod o=rx /usr/share/davical \
	&& chown -R root:apache /usr/share/awl \
	&& cd /usr/share/awl/ \
	&& find ./ -type d -exec chmod u=rwx,g=rx,o=rx '{}' \; \
	&& find ./ -type f -exec chmod u=rw,g=r,o=r '{}' \; \
	&& find ./ -type f -name *.sh -exec chmod u=rwx,g=rx,o=r '{}' \; \
	&& find ./ -type f -name *.sh -exec chmod u=rwx,g=r,o=rx '{}' \; \
	&& chmod o=rx /usr/share/awl

RUN 	mkdir -p /run/apache2 \
	&& sed -i "s/ErrorLog logs\/error.log/#ErrorLog log\/error.log/g" /etc/apache2/httpd.conf \
	&& sed -i "s/CustomLog logs\/access.log combined/#CustomLog logs\/access.log combined/g" /etc/apache2/httpd.conf

COPY 	./initialized_db.sh /sbin/initialized_db.sh
COPY	./docker-entrypoint.sh /sbin/docker-entrypoint.sh
COPY	./apache.conf /config/apache.conf
COPY	./davical.php /config/davical.php
COPY	./rsyslog.conf /config/rsyslog.conf
RUN 	chmod 0755 /sbin/initialize_db.sh \
	&& chmod 0755 /sbin/backup_db.sh  \
	&& chmod 0755 /sbin/docker-entrypoint.sh \
	&& chmod 0755 /sbin/restore_db.sh \
	&& mkdir /etc/davical /etc/supervisor.d/ /etc/rsyslog.d \
	&& /bin/echo -e "\$IncludeConfig /etc/rsyslog.d/*.conf" > /etc/rsyslog.conf \
	&& chown -R root:apache /etc/davical \
	&& chmod -R u=rwx,g=rx,o= /etc/davical \
	&& chown root:apache /config/davical.php \
	&& chmod u+rwx,g+rx /config/davical.php \
	&& ln -s /config/apache.conf /etc/apache2/conf.d/davical.conf \	
	&& ln -s /config/davical.php /etc/davical/config.php \
	&& ln -s /sbin/backup_db.sh /etc/periodic/daily/backup \
	&& ln -s /config/supervisord.conf /etc/supervisor.d/supervisord.ini \
	&& ln -s /config/rsyslog.conf /etc/rsyslog.d/rsyslog-davical.conf



EXPOSE 80
VOLUME 	["/var/lib/postgresql/data/","/config"]
ENTRYPOINT ["/sbin/docker-entrypoint.sh"]
