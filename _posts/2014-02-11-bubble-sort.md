---
layout: post
category: java语言
title: Java冒泡排序
tags: [java]
---
{% include JB/setup %}


冒泡排序（Bubble Sort）是一种简单的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。

冒泡排序算法的运作如下:

1. 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。
3. 针对所有的元素重复以上的步骤，除了最后一个。
4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

示例代码：

	public static void main(String[] args) {

		int[] arr = {9, 2, 4, 5, 7, 3, 6, 1, 8}; //待排序数组

		for(int i = 0; i < arr.length - 1; i ++) {
			for(int j = 0; j < arr.length - i - 1; j ++) { //注意j的范围
				if (arr[j] > arr[j + 1]) {
					int tmp = arr[j];
					arr[j] = arr[j + 1];
					arr[j + 1] = tmp;
				}
			}
		}
		System.out.println(Arrays.toString(arr));
	}