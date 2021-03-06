FROM phusion/baseimage

# Allow access to the container as user regis
RUN useradd -p $(openssl passwd regis) -d /home/regis -m --shell /bin/bash regis
RUN apt-get update && apt-get install sudo -y
RUN usermod -aG sudo regis

## Enable SSH
RUN rm -f /etc/service/sshd/down
RUN /etc/my_init.d/00_regen_ssh_host_keys.sh

# CouchDB config
RUN add-apt-repository ppa:couchdb/stable
RUN apt-get update && apt-get install couchdb -y
COPY couchdb-local.ini /etc/couchdb/local.ini
RUN mkdir /run/couchdb
RUN chown couchdb:couchdb -R /etc/couchdb/
RUN chown couchdb:couchdb -R /run/couchdb/
COPY couchdb-run /etc/service/couchdb/run

# WebDav config
RUN apt-get install apache2 -y
RUN mkdir /var/www/webdav
RUN chown www-data:www-data -R /var/www
RUN a2enmod dav
RUN a2enmod dav_fs
COPY webdav.htpasswd /etc/apache2/conf/webdav.htpasswd

# Apache config (previously nginx)
COPY regis-backend.conf /etc/apache2/sites-enabled/
RUN for module in "proxy proxy_http proxy_ajp proxy_wstunnel rewrite deflate headers proxy_balancer proxy_connect proxy_html"; do a2enmod $module; done
RUN a2dissite 000-default
COPY apache-run /etc/service/apache2/run

USER regis
RUN mkdir /home/regis/www
COPY www/ /var/www/html

# Webservice config

USER root
COPY regis-run /
CMD ["/regis-run"]
ENTRYPOINT ["/bin/bash"]
