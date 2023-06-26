---
layout: post
title:  "Lemmy NPM Setup Instructions"
date:   2023-06-26 17:01:57 +0000
categories: proxy
---

All settings assume you already have ports 80 and 443 open on your server.

Details tab
![NPM details page](/assets/details.png)

Custom locations tab, you will want to add entries for /api (as shown) but also add additional entries for /.well-known, /pictrs, and /feeds. All should point to the same hostname and port (lemmy:8536)

![NPM custom locations tab](/assets/custom_locations.png)

SSL tab, select these options and fill in your email address then click save. It should request a Letsencrypt cert for you and apply it to your site.

![NPM SSL tab](/assets/ssl.png)

Advamced tab
![NPM Advanced tab](/assets/advanced.png)

In the Advanced tab, paste this in and save.

{% highlight ruby %}
location / {

      # The default ports:
      # lemmy_ui_port: 1235(not used here)
      # lemmy_port: 8536
      # The cooler port:
      # lemmy_ui_port: 1236

      set $proxpass "http://lemmy_ui:1234";
      if ($http_accept ~ "^application/.*$") {
        set $proxpass "http://lemmy:8536";
      }
      if ($request_method = POST) {
        set $proxpass "http://lemmy:8536";
      }
      proxy_pass $proxpass;

      rewrite ^(.+)/+$ $1 permanent;

      # Send actual client IP upstream
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
{% endhighlight %}