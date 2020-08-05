[参考](https://www.cnblogs.com/pyyu/p/9467289.html)


### mysql (分支 mariadb)　

1. 安装mariadb
```
	-yum
	-源码编译安装
	-下载rpm安装
	yum和源码编译安装的区别？
		1.路径区别-yum安装的软件是他自定义的，源码安装的软件./configure --preifx=软件安装的绝对路径
		2.yum仓库的软件，版本可能比较低，而源码编译安装，版本可控
		3.编译安装的软件，支持第三方功能扩展./configure  这里可以加上很多参数，定制功能
		
	yum仓库的区别
		1.阿里云的yum仓库
		2.假设mysql官网，也会提供rpm包，源码包，以及yum源，供给下载
		
```
2. 配置mariadb的官方yum源，用于自动下载mariadb的rpm软件包，自动安装
```
	注意点：阿里云提供的yum仓库，和epel源仓库，它也有mariadb，但是版本可能会很低
	
	这个是yum默认的mariadb的版本信息
	mariadb                     x86_64                     1:5.5.60-1.el7_5                      base                     8.9 M
	
	
	那我们就得选用mariadb的官方yum源，
```
3. 配置官方的mariadb的yum源，手动创建 mariadb.repo仓库文件  (此步重要！！！！！！！！！！！)
```
	touch /etc/yum.repos.d/mariadb.repo 
	然后写入如下内容
	[mariadb]
	name = MariaDB
	baseurl = http://yum.mariadb.org/10.1/centos7-amd64
	gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
	gpgcheck=1
```
4. 通过yum安装mariadb软件，安装mariadb服务端和客户端  (由于是国外镜像源，因此下载速度可能很慢)
```
yum install MariaDB-server MariaDB-client -y
```


5. 如果下载速度太慢，请删除 mariadb.repo，只是为了使用阿里云的yum源中的mariadb
```
rm -rf /etc/yum.repos.d/Mariadb.repo 
然后清空yum 缓存
yum clean all 
```

6. 使用阿里云的yum下载  mariadb  
```
（阿里云的mariadb包名是小写的，而官方的是大写的！！！！注意的）
yum install mariadb-server  mariadb -y  
```
7. 安装完成后，启动mariadb服务端
```
systemctl  start/stop/restart/status  mariadb 
systemctl enable mariadb   开机启动mariadb
```

8.mysql初始化
```
mysql_secure_installation   这条命令可以初始化mysql，删除匿名用户，设置root密码等等....
```
```
Set root password? [Y/n] y

 ... Success!




Remove anonymous users? [Y/n] y
 ... Success!



Disallow root login remotely? [Y/n] n
 ... skipping.



Remove test database and access to it? [Y/n] y
 ... skipping.



Reload privilege tables now? [Y/n] y
 ... Success!



```

![image](https://img2018.cnblogs.com/blog/1132884/201810/1132884-20181013001633190-84992728.png)


![image](https://img2018.cnblogs.com/blog/1132884/201810/1132884-20181013001649899-2112300228.png)


![image](https://img2018.cnblogs.com/blog/1132884/201810/1132884-20181013001659057-1064615866.png)


![image](https://img2018.cnblogs.com/blog/1132884/201810/1132884-20181013001716166-629537215.png)


![image](https://img2018.cnblogs.com/blog/1132884/201810/1132884-20181013001732459-124817082.png)


![image](https://img2018.cnblogs.com/blog/1132884/201810/1132884-20181013001741065-1446311536.png)


9.设置mysql的中文编码支持，修改/etc/my.cnf
```
		1.   
		vi /etc/my.cnf 
		在[mysqld]中添加参数，使得mariadb服务端支持中文
		[mysqld]
		character-set-server=utf8
		collation-server=utf8_general_ci
		2.重启mariadb服务，读取my.cnf新配置
		systemctl restart mariadb 
		3.登录数据库，查看字符编码
		mysql -uroot -p 
		 
		输入 \s  查看编码 
```		

#### 高版本的设置方法
```
服务端：
vim /etc/my.cnf.d/server.cnf
在[mysqld]中添加参数，使得mariadb服务端支持中文
	[mysqld]
	character-set-server=utf8
	collation-server=utf8_general_ci
	lower_case_table_names=1 #大小写不敏感


client端
vim  /etc/my.cnf.d/client.cnf

在[client]添加参数
[client]
default-character-set=utf8

2.重启mariadb服务，读取my.cnf新配置
		systemctl restart mariadb 

```
10.mysql常用命令
```
		desc  查看表结构
		create database  数据库名
		create table  表名
		show create  database  库名  		查看如何创建db的
		show create table 表名;			查看如何创建table结构的
		
		查看数据库的编码设置，以及表的设置
	    show create database qishi1;
	    show create  table   shenqu;

		#修改mysql的密码
		set password = PASSWORD('redhat');
		
		#创建mysql的普通用户，默认权限非常低
		create user yining@'%' identified by 'yiningzhenshuai';
		
		#查询mysql数据库中的用户信息
		use mysql;
		select host,user,password  from user;
```

11.给用户添加权限命令
```
grant all privileges on *.* to 账户@主机名   　　 对所有库和所有表授权所有权限

grant all privileges on *.* to yining@'%';  给yining用户授予所有权限

flush privileges;		刷新授权表
```
12.授予远程登录的权限命令				
```
(root不能远程登录的问题？？)
grant all privileges on *.* to yining@'%';  给yining用户授予所有权限


grant all privileges on *.* to root@'%' identified by 'redhat';  #给与root权限授予远程登录的命令


此时可以在windows登录linux的数据库

mysql -uyining -p  -h  服务器的地址  			连接服务器的mysql

```
13.学习mysql的数据备份与恢复 
```
1.mysqldump -u root -p --all-databases > /data/AllMysql.dump		导出当前数据库的所有db，到一个文件中
2.登录mysql 导入数据
	mysql -u root -p 
	>   source /data/AllMysql.dump 
	
3.通过命令导入数据
mysql -uroot -p   <   /data/AllMysql.dump  #在登录时候，导入数据文件，一样可以写入数据

```

```
在linux服务端，mysql，导入knight的数据
		1.mysql数据的导出，与导入
		这个命令是在linux/windows中敲的
		mysqldump -u root -p --all-databases >  knight.dump  
		
		2.上传这个数据文件到linux数据库中
		
		3.在linux的mysql，导入这个数据文件
		mysql -uroot -p   <   /opt/knight.dump  
		
```


#### 配置文件的格式
*.conf 
*.cnf 
*.ini 
*.yml 

### MYSQL主从复制
mysql的主从复制架构，需要准备两台机器，并且可以通信，安装好2个mysql，保持版本一致性   
mysql -v 查看数据库版本



#### 主库的配置
1. 准备主库的配置文件  
```
vim /etc/my.cnf 

写入开启主库的参数
	[mysqld]
	server-id=1 			#标注 主库的身份id
	log-bin=s15mysql-bin		#那个binlog的文件名
```
2. 重启mairadb，读取配置文件
```
	systemctl restart mariadb 
```
3. 查看主库的状态
```
	mysql -uroot -p 

	show master status;  #这个命令可以查看 日志文件的名字，以及数据起始点 
```
4. 创建用于主从数据同步的账户
```
create user 'yuanhao'@'%' identified by 'yuanhaobuxitou';
```
5. 授予主从同步账号的，复制数据的权限

```
grant replication slave on *.* to 'yuanhao'@'%';
```
6. 进行数据库的锁表，防止数据写入
```
flush table with read lock;
```
7. 将数据导出 
```
mysqldump -u root -p --all-databases >  /opt/zhucong.dump
```
8. 然后将主库的数据，发送给从库
```
scp /opt/zhucong.dump   root@从库:/opt/
```
9. 此时去从库的mysql上，登录，导入主库的数据，保持数据一致性
```
mysql -uroot -p 
source /opt/zhucong.dump 
```





#### 从库的配置
1. 写入my.cnf，从库的身份信息
```
vi /etc/my.cnf 
[mysqld]
server-id=10
```
2. 检查一下主库和从库的 参数信息 
```
show variables like 'server_id';
show variables like 'log_bin';
```
3. 通过一条命令，开启主从同步
```
change master to master_host='192.168.13.78',
master_user='yuanhao',
master_password='yuanhaobuxitou',
master_log_file='s15mysql-bin.000001',
master_log_pos=571;
```

4. 开启从库的slave同步
```
start slave; 
```
5. 查看主从同步的状态
```
show slave status\G;  
```
6. 查看两条参数 ，确保主从正常
```
    Slave_IO_Running: Yes
    Slave_SQL_Running: Yes
```


# 坑

### MySQLdb._exceptions.ProgrammingError: (1146, "Table 'tgsdata.tgsAdmin_userinfo' doesn't exist")

```
有可能是表名大小写的问题

```