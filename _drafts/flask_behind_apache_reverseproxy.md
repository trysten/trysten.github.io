---
layout: post
title: Flask behind Apache ReverseProxy
categories: wsgi python

---
A few times over the years I have attempted to use apache's mod_proxy_http to expose various localhost services. Perhaps unwisely I wanted to access my flask apps from the internet. Say you have the typical flask app running (from flask) on `http://localhost:5000/` and want to expose that on `example.com/5000`. You might try an apache config like

    <Location "/5000">
    ProxyPass "http://localhost:5000"
    </Location> 

But you will find that flask's [redirect](https://flask.palletsprojects.com/en/1.1.x/api/#flask.redirect) function points your client to localhost rather than example.com. mod_proxy provides the `ProxyPassReverse` directive to modify outgoing http headers.


    <Location "/5000">
    ProxyPass "http://localhost:5000"
    ProxyPassReverse "http://localhost:5000"
    </Location> 

## SCRIPT_NAME
After reading this [very](https://gist.github.com/szul/68c9245dff2eb7e19d6c3b713ce37a09) helpful gist, I began to understand the parameters of the problem. Without more information, our app can only understand that the request is coming from the localhost (apache's ProxyPass). Some headers are set automatically, but the de-facto header for a subpath is `X-Forwarded-Prefix`. To set this header, add `RequestHeader` to your location block:

    <Location "/5000">
    ProxyPass "http://localhost:5000"
    RequestHeader set X-Forwarded-Prefix "/5000"
    </Location> 
