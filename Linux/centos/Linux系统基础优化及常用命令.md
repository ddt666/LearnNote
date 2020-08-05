[link](https://www.cnblogs.com/pyyu/articles/9355477.html)

## 创建普通用户
```
useradd 用户名 创建普通用户
passwd 用户名 更改用户密码
passwd 不加用户名 更改当前用户密码
```

## 查看用户信息
```
/etc/passwd 存放用户信息
/etc/group  存放用户组信息
通过id命令查看用户信息 
id  xiaobai
```

## 切换用户
```
su命令可以切换用户身份的需求，
su - username

su命令中间的-号很重要，意味着完全切换到新的用户，即环境变量信息也变更为新用户的信息
```

## userdel删除用户
```
-f     强制删除用户
-r    同事删除用户以及home目录
userdel -r pyyu 
```

### 用户组

```
groupadd it_dep
```

### sudo命令
sudo命令用来以其他身份来执行命令，预设的身份为root。在/etc/sudoers中设置了可执行sudo指令的用户。若其未经授权的用户企图使用sudo，则会发出警告的邮件给管理员。用户使用sudo时，必须先输入密码，之后有5分钟的有效期限，超过期限则必须重新输入密码。

语法
```
sudo 【选项】【参数】
-b：在后台执行指令；
-h：显示帮助；
-H：将HOME环境变量设为新身份的HOME环境变量；
-k：结束密码的有效期限，也就是下次再执行sudo时便需要输入密码；。
-l：列出目前用户可执行与无法执行的指令；
-p：改变询问密码的提示符号；
-s<shell>：执行指定的shell；
-u<用户>：以指定的用户作为新的身份。若不加上此参数，则预设以root作为新的身份；
-v：延长密码有效期限5分钟；
-V ：显示版本信息。

```

如果提示没有权限，  
这是由于配置sudo必须编辑/etc/sudoers文件，并且只有root才能修改，咱们可以通过visudo命令直接编辑sudoers文件，使用这个命令还可以检查语法，比直接编辑 vim /etc/sudoers更安全

```
visudo 编辑sudoers文件

写入
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
chaoge  ALL=(ALL)       ALL   #允许chaoge在任何地方，执行任何命令

```


### 设置主机名
```
[root@yugo /tmp 11:04:42]#hostnamectl set-hostname pyyuc
[root@pyyuc ~ 11:05:12]#hostname
pyyuc
```

# 文件与目录权限
Linux权限的目的是（保护账户的资料）

Linux权限主要依据三种身份来决定：

user/owner 文件使用者,文件属于哪个用户
group 属组,文件属于哪个组
others 既不是user，也不再group，就是other，其他人

### 什么是权限

```

在Linux中，每个文件都有所属的所有者，和所有组，并且规定了文件的所有者，所有组以及其他人对文件的，可读，可写，可执行等权限。

对于目录的权限来说，可读是读取目录文件列表，可写是表示在目录内新增，修改，删除文件。可执行表示可以进入目录
```
### Linux权限的观察
```
使用一条命令查看权限
ls -l /var/log/mysqld.log 

```
![image](https://images2018.cnblogs.com/blog/1132884/201808/1132884-20180811155113844-356770043.png)

解读上图：

权限，第一个字母为文件类型，后续9个字母，每3个一组，是三种身份的权限
文件链接数
文件拥有者-属主
文件拥有组-属组
文件大小
最后一次被修改的时间日期
文件名 

先来分析一下文件的类型
```

-    一般文件
d    文件夹
l    软连接（快捷方式）
b    块设备，存储媒体文件为主
c    代表键盘,鼠标等设备
```

### 文件权限
```
r    read可读，可以用cat等命令查看
w    write写入，可以编辑或者删除这个文件
x    executable    可以执行

```

### 目录权限
权限这里测试不要用root实验！！！！root太牛逼了

请用普通用户执行！！！！！测试文件、文件夹权限操作，请用普通用户！
```
r    可以对此目录执行ls列出所有文件
w    可以在这个目录创建文件
x    可以cd进入这个目录，或者查看详细信息

```

![权限与数字转化](https://images2018.cnblogs.com/blog/1132884/201808/1132884-20180823101551140-166203010.png)


```
rwx对应 4+2+1  

r  4 
w   2 
x 1 
5(user)4(group)6 (other)  转化字母   r-xr--rw-

000  ------
010  -----x---

rw-r----x    6 4  1 

777  rwxrwxrwx
```
### 修改文件权限属性
修改文件权限的命令
chmod  change  mode 缩写

方式一：字符
```
-rw-r--r--
普通文件，user rw-    group  r--   other  r-- 

chmod u+ file
-rwxr--r--
chmod g-r  file 
-rw----r--
chmod o+w file 
-rw-r--rw-
```
方式二：数字
```
-rwxrwxrwx. 1 root root  0 Dec 26 21:41 123.txt
chmod   531 123.txt

-r-x-wx--x  123.txt
531
```

### 改变用户的属主
chown  change owner 更改拥有者
```
chown  用户名  file
更改属组
chgrp  组名   file 

```

## 软连接配置

软连接也叫做符号链接，类似于windows的快捷方式。

常用于安装软件的快捷方式配置，如python，nginx等

### ln命令
```
ln -s  目标文件绝对地址  快捷方式的绝对路径地址

ln -s  /opt/cs.txt   /home/cs.txt 
```

把python3.6添加到环境变量中华，软连接和path添加，二选一即可  
方式一：
```
py3  /opt/python36/bin/python3.6  解释器绝对路径

python的时候，就去path中寻找

将python3.6的解释器，添加快捷方式到 /usr/local/sbin/python3.6

当我们输入python的时候
ln -s /opt/python36/bin/python3.6     /usr/local/sbin/
```

方式二：
```
echo $path
[root@s15fafafa home]# echo $PATH
PATH变量只能添加目录，不能定位到文件
将某个文件地下所有内容，都加入环境变量 
/usr/local/sbin
:/usr/local/bin
:/usr/sbin
:/usr/bin
:/root/bin

#假设不用这个
:/opt/python36/bin/   这才是正确的添加python环境变量

```







### tar 命令 压缩解压命令
```
-z  就是调用gzip的压缩/解压格式 
-c  压缩参数
-x  解压参数
-v  显示过程
-f  指定文件   这个参数要写在最后 
-C   大写的C是指定解压的目录

语法 
压缩文件 
tar  -cf  压缩文件名    想压缩的内容 
解压文件
tar -xf  压缩文件名
```
```
lrzsz 上传下载的小工具 

xftp 文件传输工具 
```

### netstat命令 印Linux中网络系统的状态信息，可让你得知整个Linux系统的网络情况。

 语法【选项】
 
 ```
 netstat [选项]
-t或--tcp：显示TCP传输协议的连线状况；
-u或--udp：显示UDP传输协议的连线状况；
-n或--numeric：直接使用ip地址，而不通过域名服务器；
-l或--listening：显示监控中的服务器的Socket；
-p或--programs：显示正在使用Socket的程序识别码和程序名称；
-a或--all：显示所有连线中的Socket；
 ```
 
### ps 命令查询进程 
 ```
ps -ef  |grep mysql 
ps -ef|grep  nginx
```



### 杀死进程的命令
```
kill 命令   如果你kill一个进程，死活杀不死，就加上 -9  
-9  强制终止信号(危险命令)  强制杀死进程，以及进程相关的依赖 

kill -9  uwsgi 

kill -9  mysqld 

```


### 修改linux的字符编码

1.编译字符编码的文件
```
vi /etc/locale.conf
写入如下变量
LANG="zh_CN.UTF-8"
```
2.读取这个文件，使得变量生效
```
source  读取命令，使得配置文件在系统中生效

source /etc/locale.conf 
```
3.查看系统字符编码
```
echo $LANG 
```


### df命令的磁盘空间
df命令用于显示磁盘分区上的可使用的磁盘空间。默认显示单位为KB。可以利用该命令来获取硬盘被占用了多少空间，目前还剩下多少空间等信息。

```
语法
df(选项)(参数)
-h或--human-readable：以可读性较高的方式来显示信息；
-k或--kilobytes：指定区块大小为1024字节；
-T或--print-type：显示文件系统的类型；
--help：显示帮助；
--version：显示版本信息。
```
示例
```
查看系统磁盘设备，默认是KB为单位：
df
使用-h选项以KB以上的单位来显示，可读性高：
df -h
```

### tree命令

```
tree命令以树状图列出目录的内容。

-a：显示所有文件和目录；
-A：使用ASNI绘图字符显示树状图而非以ASCII字符组合；
-C：在文件和目录清单加上色彩，便于区分各种类型；
-d：先是目录名称而非内容；
-D：列出文件或目录的更改时间；
-f：在每个文件或目录之前，显示完整的相对路径名称；
-F：在执行文件，目录，Socket，符号连接，管道名称名称，各自加上"*"，"/"，"@"，"|"号；
-g：列出文件或目录的所属群组名称，没有对应的名称时，则显示群组识别码；
-i：不以阶梯状列出文件和目录名称；
-l：<范本样式> 不显示符号范本样式的文件或目录名称；
-l：如遇到性质为符号连接的目录，直接列出该连接所指向的原始目录；
-n：不在文件和目录清单加上色彩；
-N：直接列出文件和目录名称，包括控制字符；
-p：列出权限标示；
-P：<范本样式> 只显示符合范本样式的文件和目录名称；
-q：用“？”号取代控制字符，列出文件和目录名称；
-s：列出文件和目录大小；
-t：用文件和目录的更改时间排序；
-u：列出文件或目录的拥有者名称，没有对应的名称时，则显示用户识别码；
-x：将范围局限在现行的文件系统中，若指定目录下的某些子目录，其存放于另一个文件系统上，则将该目录予以排除在寻找范围外。


```


### dns服务 

```
bind软件，配置dns服务的

常见的互联网 dns服务器 

8.8.8.8  谷歌的dns服务器
114.114.114.114  114dns服务器地址
223.5.5.5  
223.6.6.6  阿里巴巴的dns服务器地址

119.29.29.29  腾讯的dns服务器地址 

.....
```

```
linux  dns配置文件是 /etc/resolv.conf 
[root@s15fafafa home]# cat /etc/resolv.conf 
# Generated by NetworkManager
nameserver 119.29.29.29		主dns
nameserver 223.5.5.5		备dns 
 
/etc/hosts文件  本地dns强制解析的文件

[root@s15fafafa home]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.13.148  www.baidu.com


```
```
dns解析顺序：
1./etc/hosts 
2./etc/resolv.conf 


linux用于解析域名的命令

nslookup  pythonav.cn 
```


##  linux的定时任务
```
crontab  -l  查看任务
crontab -e  编辑任务
```



crontab的语法
```
所有命令一定要用绝对路径来写！


分 时  日 月 周   命令

*   *   *  *  *   每一分钟 做这件事 

#每分钟执行一次命令
* * * * *  命令

#每小时的3,15分钟执行命令

分		时		日 		月 		周		
3,15    *     	*		*		*  命令	


#在上午8-11点的第3和第15分钟执行
分		时		日 		月 		周		
3,15   8-11     *      *      *  命令



#每晚9:30执行命令
分		时		日 		月 		周		
30      21      *		*		*


#每周六、日的1：30执行命令
分		时		日 		月 		周		
30		1       *       *       6,0  


#每周一到周五的凌晨1点，清空/tmp目录的所有文件
分		时		日 		月 		周		
0       1        *     *        1-5    /usr/bin/rm -rf /tmp/*  

#每晚的21:30重启nginx
分		时		日 		月 		周		
30      21     *      *     *    /usr/bin/systemctl  restart nginx 

#每月的1,10,22日的4:45重启nginx
分		时		日 		月 		周		
45     4        1,10,22     *    *   /usr/bin/systemctl  restart nginx


#每个星期一的上午8点到11点的第3和15分钟执行命令
分		时		日 		月 		周		
3,15    8-11    *     *   	1  执行命令
```


## linux软件包管理
linux软件格式分为

1. 源码包格式
```
    1.下载python3的源码包
    2.解压缩源码包,切换目录
    3.编译且安装
    4.配置环境变量
    5.使用python3
```
2. rpm二进制包格式(这种安装方式，需要手动解决依赖关系，有可能装一个mysql，装个俩小时)
```
    1.下载软件的rpm格式包
    2. rpm -ivh lrzsz.rpm 
    3.使用lrzsz工具
    
    补充：
    1.如果直接安装mysql5.6.rpm，依赖了很多其他的软件包，我就得一个一个解决依赖，
    2.所以rpm安装方式，需要手动解决依赖关系，很麻烦，不建议使用
```
	


3. yum安装方式  
yum工具，自动的搜索下载rpm包，且安装，且解决依赖关系，自动处理下载其他的依赖rpm包

```
软件开发目录规范
lib 库文件
core  核心文件
bin  可执行文件
conf 配置文件
log  日志文件夹
readme  使用说明书
```
```
想用python的模块
pip3 install -i http://pypi.douban.com/simple flask 

想用linux的软件，yum默认去从centos官网去下载
yum install    
```
#### yum和源码编译安装的区别？
```
    1.路径区别-yum安装的软件是他自定义的，源码安装的软件./configure --preifx=软件安装的绝对路径
    2.yum仓库的软件，版本可能比较低，而源码编译安装，版本可控
    3.编译安装的软件，支持第三方功能扩展./configure  这里可以加上很多参数，定制功能
```		
#### yum仓库的区别
```
    1.阿里云的yum仓库
    2.假设mysql官网，也会提供rpm包，源码包，以及yum源，供给下载
```


#### yum源的仓库路径
```
/etc/yum.repos.d/
然后这个目录底下，只有 以 .repo结尾的文件，才会被识别为yum仓库
```


#### 配置国内的yum源
```
1.在/etc/yum.repos.d/目录底下，定制我们自己的repo仓库文件 
2.我们自己没有yum仓库，我们就去拿阿里巴巴的yum仓库
3.https://opsx.alibaba.com/mirror  这就是阿里巴巴的镜像站
4.下载阿里巴巴的yum仓库文件
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
wget下载文件后，-O参数，指定放到某个目录，且改名
5.清除yum缓存 
yum clean all 
6.生成新的阿里云的yum软件缓存
yum makecache
```

#### 再配置epel额外的仓库源，这个仓库里就存放了很多第三方软件，例如redis  mysql  nginx 
```
1.配置epel仓库
wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
2.最好再生成yum缓存
yum makecache 
3.请随心所欲的使用 yum工具
```
#### yum示例用法
```
yum安装nginx web服务器软件
1.  yum install nginx  -y       -y 一路都是默认yes
2.启动nginx  
直接输入nginx命令
3.修改nginx主页面 ，文件名字叫做 index.html  
find  /   -name index.html		查找这个文件所在地
vim /usr/share/nginx/html/index.html		修改这个nginx首页文件
```

### yum工具 
```
yum install  nginx -y  

```
```
如果你用yum命令，提示yum进程被锁定，无法使用 
解决办法： ps -ef|grep yum 进程，这是说 有另一个进程也在用yum
yum只能有一个进程使用 
```
###　吞吐量
```

django 600 
flask  1000+
tornado 异步非阻塞的框架 1800+
sanic  2800+  uvloop事件驱动   用在游戏接口领域


go 
net/http  web服务器  6W+ 
```

### 系统服务管理命令
```
只有通过yum安装的软件，默认才能使用这个命令管理 
systemctl  start/stop/restart  服务名

systemctl  start/stop/restart    mariadb 
systemctl  start/stop/restart  redis
systemctl  start/stop/restart  nginx
```