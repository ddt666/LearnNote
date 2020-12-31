## ServletContext对象

1. 概念：代表整个web应用，可以和程序的容器(服务器)来通信
2. 获取：
	1. 通过request对象获取
		request.getServletContext();
	2. 通过HttpServlet获取
		this.getServletContext();
3. 功能：
	1. **获取MIME类型**：
		
		* MIME类型:在互联网通信过程中定义的一种文件数据类型
	* 格式： 大类型/小类型   text/html		image/jpeg
		
		* 获取：String getMimeType(String file)  
	2. **域对象：共享数据**
		
		1. setAttribute(String name,Object value)
	2. getAttribute(String name)
	3. removeAttribute(String name)
		
		* ServletContext对象范围：所有用户所有请求的数据
	3. **获取文件的真实(服务器)路径**
		
		1. 方法：String getRealPath(String path)  
	   	 String b = context.getRealPath("/b.txt");*//web目录下资源访问*
	      System.out.println(b);
	   
	        String c = context.getRealPath("/WEB-INF/c.txt");*//WEB-INF目录下的资源访问*
	     System.out.println(c);
	   
	        String a = context.getRealPath("/WEB-INF/classes/a.txt");*//src目录下的资源访问*
	        System.out.println(a);
	       
	       

## 文件下载案例：

* 文件下载需求：
	1. 页面显示超链接
	2. 点击超链接后弹出下载提示框
	3. 完成图片文件下载

* 分析：
	1. 超链接指向的资源如果能够被浏览器解析，则在浏览器中展示，如果不能解析，则弹出下载提示框。不满足需求
	2. 任何资源都必须弹出下载提示框
	3. 使用响应头设置资源的打开方式：
		* content-disposition:attachment;filename=xxx

* 步骤：
	1. 定义页面，编辑超链接href属性，指向Servlet，传递资源名称filename
	2. 定义Servlet
		1. 获取文件名称
		2. 使用字节输入流加载文件进内存
		3. 指定response的响应头： content-disposition:attachment;filename=xxx
		4. 将数据写出到response输出流

* 问题：
	* 中文文件问题
		* 解决思路：
			1. 获取客户端使用的浏览器版本信息
			2. 根据不同的版本信息，设置filename的编码方式不同