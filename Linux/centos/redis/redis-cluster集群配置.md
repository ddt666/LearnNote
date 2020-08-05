[link](https://www.cnblogs.com/pyyu/p/9844093.html)

### redis-cluster安装配置
1. 准备6个redis数据库实例，准备6个配置文件redis-{7000....7005}配置文件
```
	-rw-r--r-- 1 root root 151 Jan  2 19:26 redis-7000.conf
	-rw-r--r-- 1 root root 151 Jan  2 19:27 redis-7001.conf
	-rw-r--r-- 1 root root 151 Jan  2 19:27 redis-7002.conf
	-rw-r--r-- 1 root root 151 Jan  2 19:27 redis-7003.conf
	-rw-r--r-- 1 root root 151 Jan  2 19:27 redis-7004.conf
	-rw-r--r-- 1 root root 151 Jan  2 19:27 redis-7005.conf
```
2. 启动6个redis数据库实例
```
	[root@localhost s15rediscluster]# redis-server redis-7000.conf 
	[root@localhost s15rediscluster]# redis-server redis-7001.conf 
	[root@localhost s15rediscluster]# redis-server redis-7002.conf 
	[root@localhost s15rediscluster]# redis-server redis-7003.conf 
	[root@localhost s15rediscluster]# redis-server redis-7004.conf 
	[root@localhost s15rediscluster]# redis-server redis-7005.conf 
```
3. 配置ruby语言环境，脚本一键启动redis-cluster 
	1. 下载ruby语言的源码包，编译安装
    ```
	wget https://cache.ruby-lang.org/pub/ruby/2.3/ruby-2.3.1.tar.gz
	```
	2. 解压缩
	```
	./configure --prefix=/opt/ruby/	   释放makefile
	make && make install     编译且安装
	```
	3. 下载安装ruby操作redis的模块包
	```
	wget http://rubygems.org/downloads/redis-3.3.0.gem
	```	
	4. 配置ruby的环境变量
	```
	echo $PATH
	
	vim /etc/profile
	写入最底行
	PATH=$PATH:/opt/ruby/bin/
	读取文件
	source /etc/profile 
	```
	5. 通过ruby的包管理工具去安装redis包，安装后会生成一个redis-trib.rb这个命令
	```
	一键创建redis-cluster 其实就是分配主从关系 以及 槽位分配 slot槽位分配
	/opt/redis-4.0.10/src/redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005
	```
	6. 检查节点主从状态
	```
	redis-cli -p 7000  info replication 
	```
	7. 向redis集群写入数据，查看数据流向
	```
	redis-cli -p 7000    #这里会将key自动的重定向，放到某一个节点的slot槽位中
	set  name  s15 
	set  addr shahe  
	```