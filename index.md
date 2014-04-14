---
layout: index
title: 首页
---

{% for post in site.posts limit:5 %}
	{{ post.content }}
	<p>&nbsp;</p>
	<p>&nbsp;</p>
{% endfor %}