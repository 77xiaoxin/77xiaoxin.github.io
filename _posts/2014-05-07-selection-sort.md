---
layout: post
category: java语言
title: Java选择排序
tags: [java]
---
{% include JB/setup %}

选择排序原理：每一趟从待排序的数据元素中选出最小（或最大）的一个元素，顺序放在已排好序的数列的最后，直到全部待排序的数据元素排完。 选择排序是不稳定的排序方法。

java代码：

	public static void sort(int[] list) {
		for (int i = 0; i < list.length -1; i ++) {
			int index = i; //记录下最小数的索引
			for (int j = i + 1; j < list.length ; j ++) {
				if (list[j] < list[index]) {
					index = j;
				}
			}
			//交换位置
			int tmp = list[index];
			list[index] = list[i];
			list[i] = tmp;
		}
	}