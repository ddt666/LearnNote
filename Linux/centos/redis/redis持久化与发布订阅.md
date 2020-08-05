## redis发布订阅
[link](https://www.cnblogs.com/pyyu/p/10013703.html)
```
三个角色，提供的redis命令
1.发布者
	publish  频道  消息		给频道发消息
2.订阅者
	SUBSCRIBE  频道     	订阅频道 
	PSUBSCRIBE 频道*  		支持模糊匹配的订阅
3.频道
	channel  频道名 自定义
```	
## 持久化
[link](https://www.cnblogs.com/pyyu/p/10009493.html)
### redis持久化之RDB
1.在配置文件中添加参数，开启rdb功能
```
redis.conf 写入
	port 6379
	daemonize yes
	logfile /data/6379/redis.log
	dir /data/6379
	dbfilename   s15.rdb
	save 900 1                    #rdb机制 每900秒 有1个修>改记录
	save 300 10                    #每300秒        10个修改
	记录
	save 60  10000                #每60秒内        10000修>改记录
```
2.开启redis服务端，测试rdb功能
```
redis-server redis.conf 
```

### redis持久化之aof

1. 开启aof功能，在redis.conf中添加参数
```
	port 6379
	daemonize yes
	logfile /data/6379/redis.log
	dir /data/6379
	appendonly yes
	appendfsync everysec
```
2. 启动redis服务端，指定aof功能，测试持久化数据 



### redis不重启之rdb数据切换到aof数据
1. 准备rdb的redis服务端
```
	redis-server   s15-redis.conf (注明这是在rdb持久化模式下)
```
2. 切换rdb到aof
```
redis-cli  登录redis，然后通过命令，激活aof持久化
127.0.0.1:6379>  CONFIG set appendonly yes				#用命令激活aof持久化(临时生效，注意写入到配置文件)
OK
127.0.0.1:6379> 
127.0.0.1:6379> 
127.0.0.1:6379>  CONFIG SET save "" 			#关闭rdb持久化
```
2.5 将aof操作，写入到配置文件，永久生效，下次重启后生效
```
	port 6379
	daemonize yes 
	logfile /data/6379/redis.log
	dir /data/6379   

	#dbfilename   s15.rdb
	#save 900 1  
	#save 300 10 
	#save 60  10000 
	appendonly yes
	appendfsync everysec
```



3. 测试aof数据持久化 ,杀掉redis，重新启动
```
kill 
redis-server s15-redis.conf 
```
4. 写入数据，检查aof文件
