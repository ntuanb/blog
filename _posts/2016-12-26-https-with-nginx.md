---
layout: post
title: HTTPS with Nginx
---

> Is the standard security technology for establishing an encrypted link between a web server and a browser.

Using the following link [**How To Secure Nginx with Let's Encrypt**](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-14-04),
any website in the domain can now be secured where all connections between to the server will be 
encrypted.

After installing `certbot-auto`, we can now secure any domain or subdomain. First we use the following command
{% highlight nginx %}
certbot-auto certonly -a webroot --webroot-path=/var/www/blog.tuanb.me/_site -d blog.tuanb.me
{% endhighlight %}

A certificate will created for that particular subdomain. Next we will need to edit the nginx 
domain config inside `/etc/nginx/sites-enabled/blog.tuanb.me`, remove port 80 access and add in the following:

{% highlight nginx %}
server {

    ...
    
    listen 443 ssl;

    server_name blog.tuanb.me;

    ssl_certificate /etc/letsencrypt/live/blog.tuanb.me/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/blog.tuanb.me/privkey.pem;
    include conf.s/ssl;
}

server {
    listen 80;
    server_name blog.tuanb.me;
    return 301 https://$host$request_uri;
}
{% endhighlight %}

Where `conf.s/ssl` is the ssl extra configurations

{% highlight nginx %}

ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_prefer_server_ciphers on;
ssl_dhparam /etc/ssl/certs/dhparam.pem;
ssl_ciphers [the cipher]
ssl_session_timeout 1d;
ssl_session_cache shared:SSL:50m;
ssl_stapling on;
ssl_stapling_verify on;
add_header Strict-Transport-Security max-age=15768000;

{% endhighlight %}

The steps will be needed to be replicated for each subdomain and will only take a few minutes.