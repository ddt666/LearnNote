## 编译安装python3
1. 下载python3的源码
```
	cd /opt
	yum install wget -y  安装wget命令
	wget https://www.python.org/ftp/python/3.6.2/Python-3.6.2.tgz
	
	wget https://www.python.org/ftp/python/3.7.6/Python-3.7.6.tgz
```
2. 安装python3之前，环境依赖解决
```
	通过yum安装工具包，自动处理依赖关系，每个软件包通过空格分割
	提前安装好这些软件包，日后就不会出现很多坑
	
	得保证这些依赖工具包，正确安装
	yum install gcc patch libffi-devel python-devel  zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel -y
```
3.解压缩源码包
```
		下载好python3源码包之后
		Python-3.6.2.tgz
		解压缩、
		tar命令可以解压缩 tgz格式
		tar -xvf  Python-3.6.2.tgz
```
	
4.切换源码包目录
```
		cd Python-3.6.2 
```

5.编译且安装
```
		1.释放编译文件makefile，这makefile就是用来编译且安装的
			./configure --prefix=/opt/python36/
				--prefix  指定软件的安装路径 
		2.  开始编译python3
			make
		3.编译且安装  (只有在这一步，才会生成/opt/python36)
			make install 
		4.配置python3.6的环境变量
			1.配置软连接(注意，这个和PATH配置，二选一)
				ln -s 目标文件  软连接文件
				ln -s  /opt/python36/bin/python3.6    /usr/bin/python3 
				此时还没有pip
				ln -s  /opt/python36/bin/pip3   /usr/bin/pip3 
			
			
			2.配置path环境变量 (二选一即可)
			echo $PATH查看环境变量
			/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
			#这个变量赋值操作，只是临时生效，需要写入到文件，永久生效
			 注意 ，请看这里
             注意 ，请看这里
             注意 ，请看这里
             注意 ，请看这里
             注意 ，请看这里
             注意 ，请看这里
             注意 ，请看这里
             注意 ，请看这里
             注意 ，请看这里
             注意 ，请看这里
             注意 ，请看这里
             注意 ，请看这里
             注意这里path的配置，需要将物理解释器的python，放在path最前面
			PATH=/opt/python36/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
			#linux有一个全局个人配置文件
			编辑这个文件，在最底行写入PATH
			vim /etc/profile 
			写入
			PATH=/opt/python36/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
			保存退出
			
			读一下这个/etc/profile 使得生效
			source /etc/profile
			
		5.测试linux安装一个django，
			pip3 install django 
		6.创建django项目
			django-admin startproject mysite 
		7.创建django的APP应用
			django-admin startapp   app01 
			
		8.编写视图函数，测试一个index视图
		
		9.注意修改settings.py的allow_hosts，windows方可访问linux的django项目
	```