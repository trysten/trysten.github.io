---
layout: post
title: Apache2 DeflateAlterEtag
categories:

---
* table of contents
{:toc}

I found this bug when I set up WebDAV on apache 2.4.57 to use the DAV saver on TiddlyWiki5

The spec violation is explained in apache docs for DeflateAlterETag and isn't available until 2.4.58

https://github.com/Jermolene/TiddlyWiki5/issues/7479
https://bz.apache.org/bugzilla/show_bug.cgi?id=45023
https://httpd.apache.org/docs/trunk/mod/mod_deflate.html#deflatealteretag
