FROM alpine:latest
RUN apk upgrade
RUN apk add nginx
COPY files/default.conf /etc/nginx/conf.d/default.conf
RUN mkdir -p /var/www/html
WORKDIR /var/www/html
COPY --chown=nginx:nginx /files/html/ .
EXPOSE 80
CMD [ "nginx", "-g", "pid /tmp/nginx.pid; daemon off;" ]
