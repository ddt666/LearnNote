## 主从配置
[link](https://www.cnblogs.com/pyyu/p/10012904.html)


1. 检查redis数据库信息，主从状态的命令
```
redis-cli  -p 6379  info  检查数据库信息
redis-cli  -p 6379  info  replication  检查数据库主从信息


```
2. 准备三个redis配置文件，通过端口的区分，启动三个redis数据库实例，然后配置主从复制
```
redis-6379.conf 
	port 6379
	daemonize yes
	pidfile /data/6379/redis.pid
	loglevel notice
	logfile "/data/6379/redis.log"
	dbfilename dump.rdb
	dir /data/6379
	
redis-6380.conf 
#通过命令快速生成配置文件
sed "s/6379/6380/g" redis-6379.conf > redis-6380.conf 
slaveof  127.0.0.1  6379   #指明主库的身份ip 和端口

redis-6381.conf 
#通过命令快速生成配置文件
sed "s/6379/6381/g" redis-6379.conf > redis-6381.conf 
slaveof  127.0.0.1  6379
```
3. 启动三个数据库实例，检测redis主从同步方案


4. redis主从赋值，故障手动切换，
	1. 杀死6379的主库实例
	```
	kill 主库
	```
	2. 手动切换主从身份
	```
		1.登录 redis-6380 ，通过命令，去掉自己的从库身份，等待连接
			slaoveof no one  
		2.登录redis-6381 ,通过命令，生成新的主任
			slaveof 127.0.0.1 6380  
	```
	3. 测试新的主从数据同步


### redis哨兵

[link](https://www.cnblogs.com/pyyu/p/9718679.html)
1. 什么是哨兵呢？保护redis主从集群，正常运转，当主库挂掉之后，自动的在从库中挑选新的主库，进行同步

2. redis哨兵的安装配置
	1. 准备三个redis数据库实例（三个配置文件，通过端口区分）
	```
		[root@localhost redis-4.0.10]# redis-server redis-6379.conf 
		[root@localhost redis-4.0.10]# redis-server redis-6380.conf 
		[root@localhost redis-4.0.10]# redis-server redis-6381.conf 
	```
	2. 准备三个哨兵，准备三个哨兵的配置文件(仅仅是端口的不同26379,26380,26381)
	```
	-rw-r--r--  1 root root    227 Jan  2 18:44 redis-sentinel-26379.conf
		port 26379  
		dir /var/redis/data/
		logfile "26379.log"

		sentinel monitor s15master 127.0.0.1 6379 2

		sentinel down-after-milliseconds s15master 30000

		sentinel parallel-syncs s15master 1

		sentinel failover-timeout s15master 180000
		daemonize yes


	-rw-r--r--  1 root root    227 Jan  2 18:45 redis-sentinel-26380.conf
		快速生成配置文件
		sed "s/26379/26380/g" redis-sentinel-26379.conf >  redis-sentinel-26380.conf 
	-rw-r--r--  1 root root    227 Jan  2 18:46 redis-sentinel-26381.conf
		sed "s/26379/26381/g" redis-sentinel-26379.conf >  redis-sentinel-26381.conf 
    ```
	3. 添加后台运行参数，使得三个哨兵进程，后台运行
	```
    [root@localhost redis-4.0.10]# echo "daemonize yes" >> redis-sentinel-26379.conf 
    [root@localhost redis-4.0.10]# echo "daemonize yes" >> redis-sentinel-26380.conf 
    [root@localhost redis-4.0.10]# echo "daemonize yes" >> redis-sentinel-26381.conf 
    ```
	4. 启动三个哨兵
	```
		1003  redis-sentinel redis-sentinel-26379.conf 
		1007  redis-sentinel redis-sentinel-26380.conf 
		1008  redis-sentinel redis-sentinel-26381.conf 
	```	
	5. 检查哨兵的通信状态
	```
	redis-cli -p 26379  info sentinel 
	查看结果如下之后，表示哨兵正常
		[root@localhost redis-4.0.10]# redis-cli -p 26379  info sentinel 
		# Sentinel
		sentinel_masters:1
		sentinel_tilt:0
		sentinel_running_scripts:0
		sentinel_scripts_queue_length:0
		sentinel_simulate_failure_flags:0
		master0:name=s15master,status=ok,address=127.0.0.1:6381,slaves=2,sentinels=3
    ```
	6. 杀死一个redis主库，6379节点，等待30s以内，检查6380和6381的节点状态
	```
	kill 6379主节点
	redis-cli -p 6380 info replication 
	redis-cli -p 6381 info replication 
	如果切换的主从身份之后，（原理就是更改redis的配置文件，切换主从身份）
	```
	7. 恢复6379节点的数据库，查看是否将6379添加为新的slave身份
	