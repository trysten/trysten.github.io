---
layout: post
title: Configure Apache to ProxyPass jekyll serve
categories: Apache jekyll
tags: explanation

---
* table of contents
{:toc}

### Motivation
This blog is written with [jekyll](https://jekyllrb.com/docs/) and I wanted to be able to browse the drafts version from my Apache development portal. I wanted to keep Jekyll's serve function on all the time, so I could write and view rendered posts at my leisure.

### Process
#### Configure ProxyPass 
Behind a [password-protected location](https://httpd.apache.org/docs/2.4/mod/mod_auth_form.html), I added the following configuration section:

    {% highlight apache %}
    <Location "/jekyll">
        ProxyPass "http://localhost:4000"
        ProxyPassReverse "/"
    </Location>
    {% endhighlight %}

#### Configure ProxyHTML
There were some resources in the header that used absolute URLs, so I decided to use [mod_proxy_html](https://httpd.apache.org/docs/2.4/mod/mod_proxy_html.html) to rewrite those remaining URLs. I thought about running jekyll with a temporary development configuration. Using mod proxy \_config.yaml is set up for deployment and can stay that way. 

I had to copy the default HTMLLinks from the [Apache docs](https://httpd.apache.org/docs/2.4/mod/mod_proxy_html.html#proxyhtmllinks) into a .conf file to be included (or into the `<Location>` section directly) because using it resets the definitions in that scope. I opted to use a configuration fragment on the possibility I would need the list again somewhere.

    {% highlight apache %}
    <Location "/jekyll">
        ProxyPass "http://localhost:4000"
        ProxyPassReverse "/"
        # Rewrite HTML links 
        ProxyHTMLEnable On
        ProxyHTMLLinks "use" "xlink:href"
        # Include default ProxyHTMLLinks directives
        Include "conf-available/ProxyHTMLLinks.conf"
        # rewrite all urls in the HTML to begin with our baseurl
        ProxyHTMLURLMap "/" "/jekyll/"
    </Location>
    {% endhighlight %}

