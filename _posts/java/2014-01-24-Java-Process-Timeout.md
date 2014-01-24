---
layout: archive
title: Java为Process设置超时
category: java
---

##Java为Process设置超时
前两天遇到一个问题，我用Java运行一个命令，但是这个命令的执行时间不能确定，而且经常假死，堆积在那里，还占用资源，这是我这个强迫症患者所不能容忍的。所以想设置一个超时，超过设定时间把线程kill掉，但是通过Runtime运行命令返回的Process对象没有提供设置超时的方法，不过线程（Thread）却可以设置等待时间，利用这一点，可以实现对Process的超时设置。

解决办法，将Process对象包装到一个线程当中，然后等待线程指定的时间，超时则结束线程。

线程类

	/**
	 *
	 * @ClassName: Worker
	 * @Description: TODO(这里用一句话描述这个类的作用) 包装Process的线程类
	 * @author  xinchao.wang
	 * @date 2013-11-15 下午7:02:15
	 *
	 */
	public class Worker extends Thread {
     
	    private Process process;
	    private Integer status;
 
	    /**
		 * 重写run方法
		 * (非 Javadoc)
	     * @Title: run
	     * @Description:
	     * @see java.lang.Thread#run()
	     */
	    @Override
	    public void run() {
	        // TODO Auto-generated method stub
	        try {
	            status = this.process.waitFor();
	        } catch (InterruptedException e) {
	            // TODO Auto-generated catch block
	            status = 1;
	            return;
	        }
	    }
	 
	    /**
	     * 构造方法
	     * @Title: Worker.java
	     * @Description:
	     * @param process
	     */
	    public Worker(Process process) {
	        this.process = process;
	    }
	 
	    /**
	     *
	     * @Title: getStatus
	     * @Description: TODO(这里用一句话描述这个方法的作用) 得到状态值
	     * @return   
	     * Integer
	     * @throws
	     */
	    public Integer getStatus() {
	        return status;
	    }
	     
	}

超时控制类

	import java.util.concurrent.TimeoutException;
	 
	/**
	 * 解决通过Runtime.exec()方法执行操作系统本地命令超时问题
	 * @ClassName: ProcessTimeOut
	 * @Description: TODO(这里用一句话描述这个类的作用) 为Process设定超时。
	 * @date 2013-11-15 下午5:12:36
	 *
	 */
	public class ProcessTimeOut {
	    private Process process;//Process对象
	    private long millis;//超时毫秒值
	     
	    /**
	     * @Title: start
	     * @Description: TODO(这里用一句话描述这个方法的作用) 主调方法
	     * @return
	     * @throws TimeoutException 超出指定毫秒值
	     * @throws InterruptedException 线程在等待过程中被打断
	     * int 返回Process运行结束后的状态码
	     * @throws
	     */
	    public int start() throws TimeoutException, InterruptedException {
	        Worker worker = new Worker(process);
	        worker.start();
	        try {
	            worker.join(millis); //等待millis毫秒
	            if (worker.getStatus() != null){
	                return worker.getStatus();
	            } else{
	                process.destroy();
	                throw new TimeoutException();
	            }
	        } catch (InterruptedException ex) {
	            process.destroy();
	            worker.interrupt();
	            throw ex;
	        }
	    }
	     
	    /**
	     * 构造方法
	     * @Title: ProcessTimeOut.java
	     * @Description:
	     * @param process Prcess对象
	     * @param millis 毫秒值
	     */
	    public ProcessTimeOut(Process process, long millis) {
	        this.process = process;
	        this.millis = millis;
	    }
	     
	}

使用方法

	@Test
	public void test() throws IOException {
	    Process p = Runtime.getRuntime().exec(cmd);
	    ProcessTimeOut pto = new ProcessTimeOut(p, 5000);
	    Integer status = null;
	    try {
	        status = pto.start();
	    } catch (TimeoutException e) {
	        // TODO Auto-generated catch block
	        System.out.println("超过设定的毫秒值");
	    } catch (InterruptedException e) {
	        // TODO Auto-generated catch block
	        System.out.println("线程等待过程中被打断");
	    }
	 
	    System.out.println(status);
	         
	    //得到线程输出流
	    ......
	}
