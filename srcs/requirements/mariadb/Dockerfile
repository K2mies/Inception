FROM	alpine:3.22.3

RUN	apk add --no-cache mariadb mariadb-client

COPY ./conf/mariadb_config /etc/my.cnf.d/mariadb_config.cnf 
RUN chmod 644 /etc/my.cnf.d/mariadb_config.cnf

COPY ./tools/mariadb-script.sh /usr/local/bin/mariadb-script.sh 
RUN chmod +x /usr/local/bin/mariadb-script.sh

EXPOSE 3306

ENTRYPOINT ["/usr/local/bin/mariadb-script.sh"]
