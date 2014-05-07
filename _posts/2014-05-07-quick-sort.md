---
layout: post
category: java语言
title: Java快速排序
tags: [java]
---
{% include JB/setup %}

快速排序（Quicksort）是对冒泡排序的一种改进。由C. A. R. Hoare在1962年提出。它的基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

Java代码：

	public static void sort(int[] list, int left, int right) {
		if (left > right) {
			return;
		}
		int i = left;
		int j = right;
		int tmp = list[left];
		while (i != j) {
			while (list[j] >= tmp && j > i) {
				j --;
			}
			if (j > i) {
				list[i] = list[j];
				i ++;
			}
			while (list[i] <= tmp && j > i) {
				i ++;
			}
			if (j > i) {
				list[j] = list[i];
				j --;
			}
		}
		list[i] = tmp;
		sort(list, left, i - 1);/* 递归左边 */
		sort(list, i + 1, right);/* 递归右边 */
	}