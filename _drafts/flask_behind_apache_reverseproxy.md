---
layout: post
title: Flask behind Apache ReverseProxy
categories: wsgi python

---
A few times over the years I have attempted to use Apache's mod_proxy_http [mod_proxy](https://httpd.apache.org/docs/current/mod/mod_proxy.html) to expose services for various reasons. I, perhaps foolishly, wanted to access my "hello world" flask apps from the internet. Say you have the typical flask app running [from flask](https://palletsprojects.com/p/werkzeug/) on `http://localhost:5000/` and want to expose that on `example.com/5000`. You could use Apache!

* Table of Contents
{:toc}

### ProxyPass

You might try an Apache config like

    <Location "/5000">
    ProxyPass "http://localhost:5000"
    </Location> 

### ProxyPassReverse
But you will find that flask's [redirect](https://flask.palletsprojects.com/en/1.1.x/api/#flask.redirect) function points a client to localhost rather than example.com. Mod_proxy provides the `ProxyPassReverse` directive to modify outgoing http headers.

    <Location "/5000">
    ProxyPass "http://localhost:5000"
    ProxyPassReverse "http://localhost:5000"
    </Location>

The redirects are now half handled. They will redirect to the correct host without the path we set in the first section.

## SCRIPT_NAME
After reading [this](https://gist.github.com/szul/68c9245dff2eb7e19d6c3b713ce37a09) helpful gist, I began to understand the parameters of the problem. Without more information, our app can only understand that the request is coming from the localhost (Apache's ProxyPass). Some headers are set automatically, but the de-facto header for a sub-path is `X-Forwarded-Prefix`. To set this header, add `RequestHeader` to your location block:

    <Location "/5000">
    ProxyPass "http://localhost:5000"
    ProxyPassReverse "http://localhost:5000"
    RequestHeader set X-Forwarded-Prefix "/5000"
    </Location> 



WSGI [PEP 3333](https://www.python.org/dev/peps/pep-3333/#environ-variables)
deprecates - [Middleware](https://github.com/antarctica/flask-reverse-proxy-fix)
