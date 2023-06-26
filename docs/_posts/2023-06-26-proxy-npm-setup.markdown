---
layout: post
title:  "Lemmy NPM Setup Instructions"
date:   2023-06-26 17:01:57 +0000
categories: proxy
---
WIP

In the Advanced tab, paste this in.

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