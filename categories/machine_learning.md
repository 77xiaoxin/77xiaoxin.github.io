---
layout: posts
title: »úÆ÷Ñ§Ï°Ëã·¨
---

{% for post in site.categories.machine_learning %}
<p>{{ post.date | date_to_string }}&nbsp;&nbsp;&nbsp;&nbsp;<a href="{{ post.url }}">{{ post.title }}</a><p>
{% endfor %}