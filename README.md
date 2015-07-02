# ember-proxy-example
Example SSL proxy for Ember apps using Nginx

This example makes use of [docker](https://www.docker.com/) and [docker compose](https://docs.docker.com/compose/)

The self signed SSL cert is for the domain name `homeslice.com` and you will have to host file the domain name to run the examples.

The `doccker-compose.yml` file does not expose the any ports to the ember server; all traffic is proxied through the web proxy.

All of the magic happens in the Nginx config file...

### Redirect to https
```
server {
    listen 80;

    server_name homeslice.com;
    return 301 https://$server_name$request_uri;
}
```

### Proxy to Ember
```
server {
    listen 443;

    ssl on;
    ssl_certificate /etc/ssl/homeslice.com.cert;
    ssl_certificate_key /etc/ssl/homeslice.com.key;

    server_name homeslice.com;

    location / {
        proxy_pass http://ember:4200;
    }
}
```
(note: the domain `ember` is automagically created when linking the docker containers)

### Live Reload
The live reload is the part that drove me nutz.  This section will listen to the live reload port and handles the websocket request
```
server {
    listen 35729;

    ssl on;
    ssl_certificate /etc/ssl/homeslice.com.cert;
    ssl_certificate_key /etc/ssl/homeslice.com.key;

    server_name homeslice.com;

    location / {
        proxy_pass http://ember:35729;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```
*But there's more...*

You need to add the security policy to handle the domain name
```
contentSecurityPolicy: {
  "script-src": "'self' homeslice.com:*",
  "connect-src": "'self' wss://homeslice.com:35729",
}
```

