---
layout: post
category: linux
title: linux去掉^M
tags: [linux]
---
{% include JB/setup %}

要去掉linux中^M，关键问题是不知道^M怎么打出来，只要能打出来，就可以用vim替换掉，或者用脚本处理。

^M的输入方法，先ctrl + V，然后ctrl + M。

同理，以^开头的其他字符，也可以用这个方法输入。

去掉^M，在vim中使用`:%s/^M//g`即可。