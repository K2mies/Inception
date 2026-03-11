FROM	alpine:3.22.3

RUN	apk update && apk add nginx openssl

RUN	mkdir -p /run/nginx

COPY	./conf/nginx.conf /etc/nginx/nginx.conf

COPY	./tools/nginx-script.sh /usr/local/bin/nginx-script.sh
RUN	chmod +x /usr/local/bin/nginx-script.sh

EXPOSE	443

CMD	["/usr/local/bin/nginx-script.sh"]
