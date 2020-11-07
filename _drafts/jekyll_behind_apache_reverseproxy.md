---
layout: post
title: Configure Apache to ProxyPass jekyll serve
categories: apache jekyll
toc: true

---
### Motivation
This blog is written with [jekyll](https://jekyllrb.com/docs/) and I wanted to be able to browse the drafts version from my apache development portal. I wanted to keep jekyll's serve function on all the time, so I could write and view rendered posts at my liesure.

### Process
#### config ProxyPass 
Behind a [password-protected URL](https://httpd.apache.org/docs/2.4/mod/mod_auth_form.html), I added the following configuration section:

    {% highlight apache %}
    <Location "/jekyll">
        ProxyPass "http://localhost:4000"
        ProxyPassReverse "/"
    </Location>
    {% endhighlight %}

#### configure ProxyHTML
There were some resources in the header that used absolute urls, so I decided to use [mod_proxy_html](https://httpd.apache.org/docs/2.4/mod/mod_proxy_html.html) to rewrite the remaining URLs instead of running jekyll with a temporary development configuration. That way \_config.yaml is set up for deployment and can stay that way. 

I had to copy the default HTMLLinks from the [apache docs](https://httpd.apache.org/docs/2.4/mod/mod_proxy_html.html#proxyhtmllinks) into conf file to be included or into the `<Location>` section directly because using it once resets the definitions in that scope. I opted to used a configuration fragment on the possbility I would need the list again someday. 

    {% highlight apache %}
    <Location "/jekyll">
        ProxyPass "http://localhost:4000"
        ProxyPassReverse "/"
        # Rewrite HTML links 
        ProxyHTMLEnable On
        ProxyHTMLLinks "use" "xlink:href"
        # Include default ProxyHTMLLinks directives because defining another clears definitions in current scope.
        Include "conf-available/ProxyHTMLLinks.conf"
        # rewrite all urls in the HTML to begin with our baseurl
        ProxyHTMLURLMap "/" "/jekyll/"
    </Location>
    {% endhighlight %}


