---
layout: post
category: java语言
title: java线程池
tags: [java, threadpool]
---
{% include JB/setup %}

在此简单记录一个java线程池的例子：

	package com.sep10.test;
	
	import java.util.concurrent.LinkedBlockingQueue;
	import java.util.concurrent.ThreadPoolExecutor;
	import java.util.concurrent.TimeUnit;
	
	public class TestThreadPool {
	
		public static void main(String[] args) {
	
			/*
			 * 构造一个线程池
			 * 池中所保存的线程数，包括空闲线程数量：8
			 * 池中允许的最大线程数：32
			 * 空闲线程存活时间：60
			 * timeUnit参数的单位：秒
			 * 执行前用于保持任务的队列，此队列仅保持由execute方法提交的Runnable任务
			 * 超出线程范围和队列容量而使执行被阻塞时所使用的处理程序
			 */
			ThreadPoolExecutor threadPool = new ThreadPoolExecutor(8, 32, 60, TimeUnit.SECONDS, new LinkedBlockingQueue<Runnable>(32),
					new ThreadPoolExecutor.DiscardPolicy());
			
			for (int i = 0; i < 64; i++) {
				try {
					String task = "task " + i;
					System.out.println("put " + task);
					threadPool.execute(new Task(task));
				} catch (Exception e) {
					e.printStackTrace();
				}
			}
		}
	
		public static class Task implements Runnable {
			private String name;
	
			public Task(String name) {
				this.name = name;
			}
	
			public void run() {
				System.out.println("start .." + this.name);
				try {
					//睡会儿
					Thread.sleep(5000);
				} catch (Exception e) {
					e.printStackTrace();
				}
			}
		}
	}

