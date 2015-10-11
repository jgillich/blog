---
layout: post
title: HTTPS Almost Everywhere
date: 2013-10-14
---
I have a free SSL certificate from [StartSSL](https://startssl.com/) for all my domains. To force HTTPS everywhere in Apache's httpd, I use the following virtual host:


    <VirtualHost _default_:80>
      RewriteEngine On
      RewriteCond %{HTTPS} off
      RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
    </VirtualHost>

Because it is `_default_`, we don't need a server name and it is still possible to create regular HTTP hosts by adding another virtual host. And all parameters are of course preserved when redirecting.
