---
layout: post
category: JAVAEE
title: SpringMVC拦截器使用
tags: [SpringMVC, 拦截器]
---
{% include JB/setup %}

在SpringMVC框架中使用拦截器，相对来说比用AOP简单多了，再也不用那些切点、织入。。。。。。

具体实现方法为：实现HandlerInterceptor接口，或者继承HandlerInterceptorAdapter类，然后重写相应的方法即可。

- preHandle方法：预处理，在进入controller之前执行。返回true则继续，返回false则停止。
- postHandle方法：在controller之后，渲染页面之前执行。
- afterCompletion：在页面渲染完成之后执行。

写一个验证登陆状态的例子：
	
	package com.sep10.framework.interceptor;
	
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;
	
	import org.springframework.web.servlet.handler.HandlerInterceptorAdapter;
	
	public class LoginInterceptor extends HandlerInterceptorAdapter{
		
		@Override
		public boolean preHandle(HttpServletRequest request,
				HttpServletResponse response, Object handler) throws Exception {
			// TODO Auto-generated method stub
			Object username = request.getSession().getAttribute("username");
			String path = request.getServletPath();
			if (username == null){
				if (path.matches("/login/?") || path.matches("/check/?")) {
					return true;
				} else {
					request.getRequestDispatcher("/login/").forward(request, response);
					return false;
				}
			}
			return true;
		}
	}

在`spring-servlet.xml`中配置：

	<!-- 拦截器，拦截顺序取决于配置顺序 -->
	<mvc:interceptors>
		<bean class="com.sep10.framework.interceptor.LoginInterceptor"></bean>
	</mvc:interceptors>