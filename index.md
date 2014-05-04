---
layout: page
title: 欢迎
---
{% include JB/setup %}

##站点简介
作者：{{site.author.name}}

邮箱：{{site.author.email}}

微博：
<a href="http://weibo.com/3697279945/profile?rightmod=1&wvr=5&mod=personinfo" title="采菇凉的小柴火" target="_blank">采菇凉的小柴火</a>

职位：数据挖掘工程师

本站是为了记录作者在日常工作中遇到的问题，以及学习中所积累的笔记，方便日后查询、复习使用，特制作本站。

作者于2010年毕业于石家庄经济学院计算机系，毕业后从事javaEE方向软件开发，2013年转型hadoop数据挖掘相关工作，对分布式计算有较好的理解和应用。欢迎志同道合的小伙伴们相互学习，相互交流。

本站始建于2013年9月10日，最开始搭建在Sina App Engine上，于2014年初迁移到Github，在此，特别感谢<a href="https://pages.github.com/" title="Github Pages" target="_blank">Github Pages</a>以及<a href="http://jekyllbootstrap.com/" title="Jekyll Bootstrap" target="_blank">Jekyll Bootstrap</a>。

##最新文章

<ul>
{% for post in site.posts limit:5 %}
<li><span>{{ post.date | date: "%B %e, %Y" }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>
