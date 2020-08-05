

[redis相关](https://www.cnblogs.com/pyyu/p/9843950.html)


![image](https://img2018.cnblogs.com/blog/1132884/201809/1132884-20180922122242786-528252216.png)


### 在linux安装redis
## 通过源码编译安装redis(不建议用yum安装)（不要使用systemctl 命令管理redis）
1. 下载源码包
```
	wget http://download.redis.io/releases/redis-4.0.10.tar.gz
	
```
2. 解压缩redis
```	
	tar -zxf redis-4.0.10.tar.gz
```
3. 进入redis源码，直接可以编译且安装
```
	make && make install 
```	
4. 可以指定配置文件启动redis
```	
	vim /opt/redis-4.0.10/redis.conf 
	1.更改bind参数，让redis可以远程访问
		bind 0.0.0.0
	2.更改redis的默认端口
		port 6380
	3.使用redis的密码进行登录
		requirepass 登录redis的密码
	4.指定配置文件启动
		redis-server redis.conf 
```
5. 通过新的端口和密码登录redis
```
redis-cli -p 6380
登录后
auth 密码

redis还支持交互式的参数，登录数据库
redis-cli -p 6380  -a  redis的密码  （这个不太安全）
```
6. 通过登录redis，用命令查看redis的密码
```

config set  requirepass  新的密码     	#设置新密码
config get  requirepass  			#获取当前的密码
```