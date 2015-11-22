---
title: CORS Proxying with nginx
date: 2015-11-22
---

CORS is a very advanced security technology designed to waste your time. It works for production environments,
but oh Firefox can I please just send some requests to that API to test
my app? The answer is no, so this is how to configure nginx and make your local
dev environment so much more secure:

    server {
      listen 8080;
      location / {
        if ($request_method = 'OPTIONS') {
          add_header 'Access-Control-Allow-Origin' '*';
          add_header 'Access-Control-Allow-Credentials' 'true';
          add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
          add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Typ';
          add_header 'Content-Type' 'text/plain charset=UTF-8';
          add_header 'Content-Length' 0;
          return 204;
        }

        add_header 'Access-Control-Allow-Origin' '*' always;
        add_header 'Access-Control-Allow-Credentials' 'true' always;
        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS' always;
        add_header 'Access-Control-Allow-Headers' 'DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Typ' always;

        proxy_redirect off;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://127.0.0.1:1234;
      }
    }

To make your life extra difficult, the creators decided you can't use wildcards for `Access-Control-Allow-Headers`, enjoy changing this config for any new headers you want to use. Oh, and please don't run this in production, please?
