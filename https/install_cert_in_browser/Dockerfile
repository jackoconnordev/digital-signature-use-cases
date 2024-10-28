FROM nginx:latest
COPY ./index.html /usr/share/nginx/html
COPY ./nginx.conf /etc/nginx/conf.d/default.conf
COPY ./server-certs/myServer_private.key ./server-certs/myServer_signed.crt /etc/nginx/certs/
