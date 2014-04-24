---
layout: posts
title: Maven
---

{% for post in site.categories.maven %}
<p>{{ post.date | date_to_string }}&nbsp;&nbsp;&nbsp;&nbsp;<a href="{{ post.url }}">{{ post.title }}</a><p>
{% endfor %}