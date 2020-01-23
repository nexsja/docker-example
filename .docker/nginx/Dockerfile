FROM nginx:1.17.6

ADD nginx.conf /etc/nginx/nginx.conf

RUN mkdir -p /etc/ssl/private /etc/ssl/certs
RUN mkdir /etc/nginx/snippets

COPY --chown=root ssl/nginx-selfsigned.crt /etc/ssl/certs
COPY ssl/nginx-selfsigned.key /etc/ssl/private
COPY ssl/dhparam.pem /etc/nginx
COPY ssl/ssl-params.conf /etc/nginx/snippets

EXPOSE 80