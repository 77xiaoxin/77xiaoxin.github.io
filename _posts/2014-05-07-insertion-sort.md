---
layout: post
category: java语言
title: Java插入排序
tags: [java]
---
{% include JB/setup %}

插入排序的基本操作就是将一个数据插入到已经排好序的有序数据中，从而得到一个新的、个数加一的有序数据，算法适用于少量数据的排序，时间复杂度为O(n^2)。是稳定的排序方法。

Java代码：

	public static void sort(int[] list) {
		for (int i = 1; i < list.length; i ++) {
			int insert = list[i];
			
			int index = i;
			while (index > 0 && insert < list[index - 1]) {
				list[index] = list[index - 1]; //将第index个元素向后移动
				index--; //递减
			}
			
			list[index] = insert;
		}
	}