---
layout: posts
title: java
---

{% for post in site.categories.java %}
	<p>
		{{ post.date | date_to_string }}&nbsp;&nbsp;&nbsp;&nbsp;<a href="{{ post.url }}">{{ post.title }}</a>
	<p>
{% endfor %}