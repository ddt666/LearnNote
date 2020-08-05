

windows中，访问项目，python  manage.py  runserver  ,默认运行在本地的http://127.0.0.1:8000/  端口



1.部署knight项目前提
	-熟悉linux操作
	-上传knight项目到linux服务器
		-使用xftp工具，进项上传文件夹，将knight代码，传到linux服务器当中

	-完成python3解释器的安装
	
	
	-完成virtualenvwrapper工具的配置，解决虚拟环境问题
		1.新建一个knight虚拟环境用于运行  crm项目
	
	
	-完成centos7下安装配置mariadb数据库，且创建数据库数据，迁移导入knight数据
		#centos7底下，mariadb就是mysql数据库，只是包名的不同
		yum install mariadb-server  mariadb  -y  
		#启动mariadb服务端
		systemctl start mariadb 
		#使用客户端去链接mysql服务端
		1.mysql -uroot -p 
		注意，linux的数据库，需要对root用户设置远程链接的权限
		grant all privileges on *.* to root@'%' identified by 'redhat';
		#刷新授权表
		flush privileges;
		授权所有的权限，在所有库，所有表  对  root用户在所有的主机上， 设置权限密码是  redhat  
		
		注意2，linux的防火墙要给关闭，否则windows去链接linux的3306端口可能被拒绝
			
		
		在linux服务端，mysql，导入knight的数据
		1.mysql数据的导出，与导入
		这个命令是在linux/windows中敲的
		mysqldump -u root -p --all-databases >  knight.dump  
		
		2.上传这个数据文件到linux数据库中
		
		3.在linux的mysql，导入这个数据文件
		mysql -uroot -p   <   /opt/knight.dump  
		
		
		
	-测试使用linux的python解释器去运行项目(注意要解决解释器的模块问题，才能正常运转项目)
		python3 manage.py runserver 0.0.0.0:8000
	
	-完成nginx的安装配置，了解nginx.conf如何配置

		
		
	-完成uWSGI命令学习，使用uWSGI启动knight项目，支持多进程
	
	-完成nginx处理knight项目的静态文件
	
	-最终效果
	
	访问nginx的80端口，即可找到knight应用，且保证静态文件页面正常



django自己也能运行一个web界面，但是默认用的wsgiref单机模块，性能比较低
uwsgi + django  + nginx 


	
nginx+uwsgi+django+supervisor


wsgi 
是一个web应用协议

基于wsgi运行的 web框架
wsgi运行的框架有bottle,DJango,Flask,用于解析动态HTTP请求

基于wsgi运行的web服务器是什么 ,才是提供wbe界面给与访问 的

wsgiref
        python自带的web服务器
    Gunicorn
        用于linux的 python wsgi Http服务器，常用于各种django，flask结合部署服务器。
    mode_wsgi
        实现了Apache与wsgi应用程序的结合
    uWSGI
        C语言开发，快速，自我修复，开发人员友好的WSGI服务器，用于Python Web应用程序的专业部署和开发

	
uwsgi在运行django项目的时候，必须找到django的wsgi.py文件，的文件内容中的参数
application = get_wsgi_application()

	
no application  .....
#解决办法就是 uwsgi，一定要找打wsgi.py这个文件



为什么要用nginx
192.168.11.37:80   >   192.168.11.37:8000
admin
css  js  jpg 

static/
.....


192.168.11.37:80/  

location / {
	#include也是一个包含语法,将uwsgi_params中的参数，导入到nginx转发请求中
	#uwsgi_params这个文件默认在 nginx112/conf/ 
    include    uwsgi_params;
	#proxy_pass  nginx接收到请求，直接转发请求给另一台机器去处理
	#他也是反向代理，并且是支持nginx和uwsgi之间
    uwsgi_pass localhost:9000;
}


安装uwsgi这个支持并发的web服务器，去启动django
1.安装uwsgi

    pip3 install uwsgi 

2.使用学习uwsgi命令，如何启动python应用

    通过uwsgi运行一个python web文件
    touch test.py 写入如下内容
    def application(env, start_response):
        start_response('200 OK', [('Content-Type','text/html')])
        return [b"i am  alex"] # python3
3.通过命令去启动python web应用 

    uwsg --http :8000   --wsgi-file  test.py 
    	--http 指定http协议
    	--wsgi-file  指定一个支持python wsgi协议的文件
	
4.通过uwsgi启动django项目(注意这里要进入knight项目目录)

    `uwsgi --http :8000 --module  knight.wsgi  


5.uwsgi自动重启python程序

    uwsgi --http :8000  --module  knight.wsgi  --py-autoreload=1 

6.使用uwsgi.ini配置文件去启动项目，这个文件自己去创建即可，放哪都可以

    (knight) [root@qishione knight]# cat uwsgi.ini 
    [uwsgi]
    # Django-related settings
    # the base directory (full path)
    #写上项目的绝对路径  
    chdir           = /opt/knight
    # Django's wsgi file
    
    #填写找到django的wsgi文件，填写相对路径，以chdir参数为相对路径
    module          = knight.wsgi
    # the virtualenv (full path)
    #填写虚拟环境的绝对路径
    home            = /root/Envs/knight/
    # process-related settings
    # master
    #启动uwsgi主进程
    master          = true
    # maximum number of worker processes
    processes       = 5
    
    #如果你使用了nginx，做反向代理，必须填写socket链接，而不是http参数
    # the socket (use the full path to be safe
    #socket          = 0.0.0.0:8000
    
    #如果你不用nginx，直接使用uwsgi，运行一个http服务端，就用这个http参数
    http = 0.0.0.0:8000
    
    
    # ... with appropriate permissions - may be needed
    # chmod-socket    = 664
    # clear environment on exit
    vacuum          = true



7. 指定配置文件去启动uwsgi
uwsgi --ini  uwsgi.ini  



8.使用ngixn处理django的静态文件
	1.设置django的静态文件目录，收集一下
	修改knight/settings.py ，写入如下参数
	STATIC_ROOT= '/opt/static'
	2.使用命令收集django的静态文件
	python3 manage.py collectstatic
	3.查看django的静态文件收集目录
	ls /opt/static
	
	4.配置nginx，反向代理，找到uwsgi项目，且配置nginx处理uwsgi的静态文件
	nginx.conf 修改配置如下
	
	    server {
        listen       80;
        server_name  qishijd.com;
        #只要用户访问qishijd.com:80/  就走这个location匹配>，反向代理给uwsgi:
        location / {
			include    uwsgi_params;
			uwsgi_pass  0.0.0.0:8000;
					}
			#当用户请求是qishijd.com/static/的时候
			#就会进入这个location匹配
			#通过alias参数进行路径别名，让nginx去/opt/static底下去找静>态资源
			location  /static  {
			alias  /opt/static;
}
    }
	
9.访问域名或者ip，查看项目
qishijd.com/login  查看静态页面是否正常


10. 公司会用一个进程管理工具，去启动，管理多个项目,supervisor


