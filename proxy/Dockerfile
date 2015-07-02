FROM nginx

RUN rm /etc/nginx/conf.d/default.conf
COPY default.conf /etc/nginx/conf.d/default.conf

COPY homeslice.com.cert /etc/ssl/homeslice.com.cert
COPY homeslice.com.key /etc/ssl/homeslice.com.key
