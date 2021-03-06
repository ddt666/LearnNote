## Tomcat：web服务器软件

- Tomcat目录结构

<img src="assets/tomcat目录结构.png" alt="tomcat目录结构"  />

1. 下载：http://tomcat.apache.org/
2. 安装：解压压缩包即可。
  * 注意：安装目录建议不要有中文和空格
3. 卸载：删除目录就行了
4. 启动：
  * bin/startup.bat ,双击运行该文件即可
  * 访问：浏览器输入：http://localhost:8080 回车访问自己

​			  http://别人的ip:8080 访问别人

  * 可能遇到的问题：

1. 黑窗口一闪而过：
	* 原因： 没有正确配置JAVA_HOME环境变量
	* 解决方案：正确配置JAVA_HOME环境变量

2. 启动报错：
	1. 暴力：找到占用的端口号，并且找到对应的进程，杀死该进程
		* netstat -ano
	2. 温柔：修改自身的端口号
		* conf/server.xml
		* `<Connector port="8888" protocol="HTTP/1.1"
             connectionTimeout="20000"
             redirectPort="8445" />`
		* 一般会将tomcat的默认端口号修改为80。80端口号是http协议的默认端口号。
			* 好处：在访问时，就不用输入端口号

5. 关闭：
  1. 正常关闭：

  	* bin/shutdown.bat
  	* ctrl+c
  2. 强制关闭：

  	* 点击启动窗口的×
6. 配置:
  * 部署项目的方式：
1. 直接将项目放到webapps目录下即可。
	* /hello：项目的访问路径-->虚拟目录
	* 简化部署：将项目打成一个war包，再将war包放置到webapps目录下。
		* war包会自动解压缩

2. 配置conf/server.xml文件
	在`<Host>`标签体中配置
	`<Context docBase="D:\hello" path="/hehe" />`
	* docBase:项目存放的路径
	* path：虚拟目录

3. 在conf\Catalina\localhost创建任意名称的xml文件。在文件中编写
	`<Context docBase="D:\hello" />`
	* 虚拟目录：xml文件的名称

  * 静态项目和动态项目：
  	* 目录结构
    		* java动态项目的目录结构：

​		-- 项目的根目录
​			-- WEB-INF目录：
​				-- web.xml：web项目的核心配置文件
​				-- classes目录：放置字节码文件的目录
​				-- lib目录：放置依赖的jar包

- 将Tomcat集成到IDEA中，并且创建JavaEE的项目，部署项目。

## IDEA与tomcat的相关配置

1. IDEA会为每一个tomcat部署的项目单独建立一份配置文件
	* 查看控制台的log：Using CATALINA_BASE:   "C:\Users\fqy\.IntelliJIdea2018.1\system\tomcat\_itcast"

2. 工作空间项目    和     tomcat部署的web项目
	* tomcat真正访问的是“tomcat部署的web项目”，"tomcat部署的web项目"对应着"工作空间项目" 的web目录下的所有资源
	* WEB-INF目录下的资源不能被浏览器直接访问。
3. 断点调试：使用"小虫子"启动 dubug 启动