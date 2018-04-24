---
layout: post
title: Self Signed Certs
---

For some reason, its a bit hard to find a place where I can copy paste a few lines to generate a self signed certificate. Placing this here so that I can remember how to generate a self signed certificate.

{% highlight linux %}

openssl genrsa -des3 -passout pass:x -out server.pass.key 2048
openssl rsa -passin pass:x -in server.pass.key -out server.key
openssl req -new -key server.key -out server.csr
penssl x509 -req -sha256 -days 365 -in server.csr -signkey server.key -out server.crt

{% endhighlight %}
