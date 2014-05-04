---
layout: post
category: 页面抓取
title: 抓取js加载的页面内容
tags: [selenium, 爬虫, phantomjs]
---
{% include JB/setup %}


###准备

selenium是一个优秀的自动化测试工具，相信搞测试的同学们应该比较熟悉了。

官网下载地址：http://docs.seleniumhq.org/download/

下载selenium最新版，根据你所熟悉的开发语言选择不同的版本，支持Java、C#、Ruby、Python等，本文使用Java。

phantomjs是一个基于webkit的无界面浏览器，也许正因为它没有界面，所以速度比FireFox，Chrome，IE等浏览器要快一些。

官方网站：http://phantomjs.org/

根据开发平台选择不同的版本，我准备了windows版本和linux两个版本，分别用作开发环境和生产环境，下载完成解压缩，放在任意目录下即可。

###开始

selenium解压开，里面有一堆jar包，在eclipse新建一个工程，将需要的jar包引入到工程中，大致需要的jar包如下：

1. commons-exec-xxx.jar
2. commons-io-xxx.jar
3. commons-logging-xxx.jar
4. guava-xxx.jar
5. httpclient-xxx.jar
6. httpcore-xxx.jar
7. json-xxx.jar
8. phantomjsdriver-xxx.jar
9. selenium-java-xxx.jar

各位同学可以在开发过程中，根据提示添加其他依赖jar包。

首先实例化一个“浏览器”：

	DesiredCapabilities capabilities = new DesiredCapabilities();
	capabilities.setCapability("phantomjs.binary.path", "F:/phantomjs/phantomjs.exe");
	capabilities.setCapability("phantomjs.page.settings.userAgent", "sep10 spider");
	capabilities.setCapability("phantomjs.page.settings.loadImages", false);
	capabilities.setJavascriptEnabled(true);

	WebDriver browser = new PhantomJSDriver(capabilities);

有了“浏览器”，就可以试着打开一些页面了：

	browser.get("http://sep10.com");

这时候，phantomjs会请求给定的网址，并渲染页面，执行js代码，然后把内容保存在内存中，可以通过以下方法对得到的内容进行分析：

	//通过元素id查找
	WebElement logo = browser.findElement(By.id("logo"));
	//通过标签名查找
	List<WebElement> divs = browser.findElements(By.tagName("div"));
	//通过class名查找
	List<WebElement> containers = browser.findElements(By.className("container"));
	//通过xpath方式查找，得到所有含有href属性的a标签
	List<WebElement> a = browser.findElements(By.xpath("//a[@href]")); 
	//还可以通过css样式选择，通过name属性查找等等。
	......

有了WebElement，可以对其进一步查找，了解过DOM模型的人，肯定不陌生，这里还可以获取元素的属性和文本：

	String id = logo.getAttribute("id");
	String text = logo.getText();

由于phantomjs是没有界面的，有的时候我们看不到phantomjs到底打开了一个怎样的页面，这时候可以截个图看一下：

	File f = ((TakesScreenshot)browser).getScreenshotAs(OutputType.FILE);
	FileUtils.copyFile(f, new File("E:/sep10.com.jpg"));

值得一提的是，selenium在截图时，无论页面有多么的长，都会完整的截取下来。

最后，一定要记得关闭“浏览器”，否则即使程序停止了，phantomjs的进程依然会存在。

	browser.quit();

###实践

根据上面的介绍，我们来小小的试验一下。

细心的同学们可能会发现，使用HttpClient抓取内容，遇到异步加载的页面就显得力不从心了，比如京东商城的价格那部分，下面写个程序试验一下。

	public static void main(String[] args) {
		DesiredCapabilities capabilities = new DesiredCapabilities();
		capabilities.setCapability("phantomjs.binary.path", "F:/phantomjs/phantomjs.exe");
		capabilities.setJavascriptEnabled(true);
		WebDriver browser = new PhantomJSDriver(capabilities);
		browser.get("http://item.jd.com/999872.html");
		WebElement price = browser.findElement(By.id("jd-price"));
		System.out.println(price.getText());
		browser.quit();
	}

输出：

	￥12898.00


###参考文档

selenium官方文档：http://docs.seleniumhq.org/docs/