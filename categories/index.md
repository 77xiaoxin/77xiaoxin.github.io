---
layout: posts
title: 文章列表
---

{% for post in site.posts %}
	<p>
		{{ post.date | date_to_string }}&nbsp;&nbsp;&nbsp;&nbsp;<a href="{{ post.url }}">{{ post.title }}</a>
	<p>
{% endfor %}