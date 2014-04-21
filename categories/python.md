---
layout: posts
title: Python学习
---

{% for post in site.categories.python %}
<p>{{ post.date | date_to_string }}&nbsp;&nbsp;&nbsp;&nbsp;<a href="{{ post.url }}">{{ post.title }}</a><p>
{% endfor %}