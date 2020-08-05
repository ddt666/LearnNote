[link](https://www.bilibili.com/video/av76265971?p=694)


使用ssh命令，远程连接服务器  

    ssh root@192.168.1.200
    
    
# Linux命令行的组成结构
![image](https://images2018.cnblogs.com/blog/1132884/201807/1132884-20180719175259770-1837332137.png)
 
 
 > 如何修改命令提示符
 
 ```
 默认是这样
[root@localhost tmp]# echo $PS1
[\u@\h \W]\$
PS1变量来控制
 
\u  当前用户
\W  最后一位工作目录
\w  绝对路径工作目录
\t  显示24h的时间
\h  主机名

PS1变量来控制
[root@localhost tmp]# PS1="[\u@\h \w \t]\$"

 
 ```
 
 ##　绝对路径与相对路径
 
 ```
 绝对路径：由根目录(/)为开始写起的文件名或者目录名称，如/home/oldboy/test.py;
相对路径：相对于目前路径的文件名写法。例如./home/oldboy/exam.py或../../home/oldboy/exam.py，简单来说只要开头不是/，就是属于相对路径
 ```
 
 ```
 linux文件目录结构
 没有盘符的概念，只有一个统一的入口
 叫做 根目录 “/”
 且目录分隔符也是 "/" 区别的windows的 "\"
 ```
![image](https://img2018.cnblogs.com/blog/1132884/201809/1132884-20180921150307683-759085586.png)

```
重点
/root
/home
/etc
/var 存放日志类型的文件

有关bin这个单词的目录都是存放可执行命令的目录
/sbin
/bin

/lib
/opt 存放第三方软件的目录（python3 ngnix mysql redis）
```
## linux 文件目录颜色
```
蓝色    代表文件夹
白色    代表普通文件
绿色    代表可执行命令

```


```
.    当前目录
..    上一层目录
-    前一个工作目录
~    当前【用户】所在的家目录，直接切换到用户文件夹下
>   重定向输出符 echo "i am happy"  >  filename.txt
    echo "xxxxxx" >  test.txt 覆盖写入
    echo "xxxxxxx" >> test.txt 追加写入
```


# 增
```
echo 相当于print
mkdir 深圳1期 创建文件夹
mkdir -p /tmp/luffy/onepiece 递归创建文件夹
mkdir 文件夹1 文件夹2 文件夹3 一次性创建多个文件夹
mkdir -p /tmp/luffy/{suolong,qiaoba,namei} 递归创建三个同级文件夹

touch 创建一个文件
touch /tmp/onepiecr.py  指定路径创建一个文件

useradd 用户名 创建普通用户
passwd 用户名 更改用户密码

```
# 删
```
rm xxx.txt remove 删除文件
rm -i 默认删除提示

慎用！！
rm -rf 操作对象
    -r  删除目录
    -f  强制性删除
    
删除  > remove > rm
参数  -i  需要删除确认
　　　-f  强制删除
     -r  递归删除目录和内容
     
cd /tmp
rm oldboy.py
#默认有提示删除，需要输入y
rm -f oldboy.py #不需要提示,强制删除
#rm默认无法删除目录，需要跟上参数-r
rm -rf /tmp/oldboy/

--------
友情提醒:初学者使用rm命令，随时快照虚拟机
    
    
```
# 改

```
cd  更改目录(change directory)
mv xxx.txt ../..  move移动命令
mv xxx.py ooo.py 重命名功能



```
# 查
```
ls      查看文件夹信息 显示文件夹的内容
    -l  以列表显示文件夹内容，详细信息  简写ll
    -la 显示目录内容的详细信息，且显示隐藏文件
    -a  显示隐藏内容
    
ls -lh  查看文件夹大小，以友好的单位形式，给用户看 kb mb G

du -h ./* 统计当前目录所有文件的大小
pwd 我在哪？(print work directory)


which ls 查看命令的绝对路径
whereis
whoami
w       查看有几个终端连接 默认有7个终端 使用ctrl+alt+f1~f7切换
ps      查看进程

```
### cat 查看文本内容
```
#查看文件，显示行号
cat -n xxx.py
#猫,查看文件
cat xxx.py

#在每一行的结尾加上$符
[root@master tmp]# cat -E 1.txt

#追加文字到文件
cat >>/tmp/oldboy.txt << EOF
唧唧复唧唧
木兰开飞机
开的什么机
波音747
EOF

```
### more命令
```
1.more命令用于查看内容较多的文本，例如要看一个很长的配置文件，cat查看内容屏幕会快速翻滚到结尾。

2.more命令查看文本会以百分比形式告知已经看到了多少，使用回车键向下读取内容

more /etc/passwd
按下空格space是翻页
按下b键是上一页
回车键向下读取内容

```
### 复制(拷贝)命令

```
复制 > copy > cp

#移动xxx.py到/tmp目录下
cp xxx.py /tmp/

#移动xxx.py顺便改名为chaoge.py
cp xxx.py /tmp/chaoge.py

Linux下面很多命令，一般没有办法直接处理文件夹,因此需要加上（参数） 
cp -r 递归,复制目录以及目录的子孙后代

cp -p 复制文件，同时保持文件属性不变    可以用stat

cp -a 相当于-pdr

#递归复制test文件夹，为test2
cp -r test test2

cp是个好命令，操作文件前，先备份
cp main.py main.py.bak

```

### vi，vim命令

```
vi，相当于windows的记事本
vim，相当notepad++

```

```
1 vim onepiece.py
2 输入i，进入编辑模式
3 开始编辑
4 按下"esc"回到命令模式，退出编辑模式
5 输入 :wq! 写入保存代码
    : 进入底线命令模式
    :w 讲文件内容写入
    :q 退出不保存
    :! 强制

```


# 快捷键
```
tab 命令补全
claar 清屏
logout 退出当前会话
ctrl + shift + r 快速登录上一次的会话

```

# 变量

linux的变量定义，不能有空格

pm="好困啊"
echo $pm $提取pm

#　PATH 环境变量
> 环境变量有优先级的！！！！
> 环境变量有优先级的！！！
> 环境变量有优先级的！！！

```
// 取出PATH的值
[root@localhost tmp]# echo $PATH 
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin


```

### 查看命令的帮助手册
```
ls --help 查看简略手册

man cp 查看详细手册 按q退出
```

### 别名alias命令

```
Linux如何提示你，在使用这些命令时候，提醒你小心呢？
#查看系统别名
alias
默认别名

alias cp='cp -i'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
别名作用是：
我们在linux中使用cp时候相当于执行了cp -i
-i：删除已有文件或目录之前先询问用户；
#别名用比较危险的操作,防止你犯错

```
### 为rm设置别名
```
#让系统显示 do not use rm
echo do not use rm
#设置rm别名
alias rm='echo do not use rm'
#设置别名永久生效,写入到/etc/profile(针对登录用户的合同，设置环境变量)
vim /etc/profile #编辑文件
G　　快速到达最后一行
o　　当前行下一行，创建一个新行，进入编辑模式
source /etc/profile #读取文件（合同生效）
---------------
#取消别名
unalias rm

```
### find查找命令
```
#Linux里如何找到需要的文件 例如 oldboy.py
find 在哪里(目录) 什么类型（文件类型） 叫什么名字（文件名）
参数

-name 按照文件名查找文件
-type 查找某一类型的文件，诸如：
b - 块设备文件。
d - 目录。
c - 字符设备文件。
p - 管道文件。
l - 符号链接文件。
f - 普通文件。
s - socket文件


find /tmp/ -type f  -name "oldboy.py"

#找出/tmp所有以 .txt 结尾的文件
find /tmp/ -type f -name "*.txt"

#找到/etc下所有名字以host开头的文件
find /etc -name 'host*'

#找到/opt上一个名为settings.py
find /opt -name 'settings.py'

```

### | 管道命令
```
Linux提供的管道符“|”讲两条命令隔开，管道符左边命令的输出会作为管道符右边命令的输入。
常见用法：
#检查python程序是否启动
ps -ef|grep "python"

#找到/tmp目录下所有txt文件
ls /tmp|grep '.txt'

#检查nginx的端口是否存活
netstat -tunlp |grep nginx

```


```
$netstat -tunlp 查看端口占用情况

```

###　grep
(global search regular expression(RE) and print out the line,全面搜索正则表达式并把行打印出来)是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。


```
语法：
grep [参数] [--color=auto] [字符串] filename
参数详解:
-i : 忽略大小写
-n : 输出行号
-v : 反向选择
--color = auto : 给关键词部分添加颜色

grep "我要找什么" /tmp/oldboy.txt
#排除 -v，排除我要找的东西
grep -v "我要找什么 /tmp/oldboy.txt

```
例题，找出/etc/passwd下root用户所在行，以及行号，显示颜色
```
cat /etc/passwd |grep '^root' --color=auto -n
```
找出/etc/passwd所有不允许登录的用户
```
grep /sbin/nologin /etc/passwd
```
找到/etc/passwd的所有与mysql有关行，行号
```
cat /etc/passwd |grep 'mysql' -n
```


### head、tail命令

```
head显示文件前几行，默认前10行
tail显示文件后几行，默认后10行
#查看前两行
head -2 /tmp/oldboy.txt
#查看后两行
tail -2 /tmp/oldboy.txt
#持续刷新显示
tail -f xx.log

#显示文件10-30行
head -30 /tmp/oldboy.txt |tail -21
```

### sed

```
sed

sed是一种流编辑器，它是文本处理中非常中的工具，能够完美的配合正则表达式使用，功能不同凡响。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space），接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有 改变，除非你使用重定向存储输出。Sed主要用来自动编辑一个或多个文件；简化对文件的反复操作；编写转换程序等。

命令格式

sed [options] 'command' file(s)
sed [options] -f scriptfile file(s)
选项

-e<script>或--expression=<script>：以选项中的指定的script来处理输入的文本文件；
-f<script文件>或--file=<script文件>：以选项中指定的script文件来处理输入的文本文件；
-h或--help：显示帮助；
-n或--quiet或——silent：仅显示script处理后的结果；
-V或--version：显示版本信息。
-i ∶插入， i 的后面可以接字串
sed命令

a\ 在当前行下面插入文本。
i\ 在当前行上面插入文本。
c\ 把选定的行改为新的文本。
d 删除，删除选择的行。
D 删除模板块的第一行。
s 替换指定字符
h 拷贝模板块的内容到内存中的缓冲区。
H 追加模板块的内容到内存中的缓冲区。
g 获得内存缓冲区的内容，并替代当前模板块中的文本。
G 获得内存缓冲区的内容，并追加到当前模板块文本的后面。
l 列表不能打印字符的清单。
n 读取下一个输入行，用下一个命令处理新的行而不是用第一个命令。
N 追加下一个输入行到模板块后面并在二者间嵌入一个新行，改变当前行号码。
p 打印模板块的行。
P(大写) 打印模板块的第一行。
q 退出Sed。
b lable 分支到脚本中带有标记的地方，如果分支不存在则分支到脚本的末尾。
r file 从file中读行。
t label if分支，从最后一行开始，条件一旦满足或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。
T label 错误分支，从最后一行开始，一旦发生错误或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。
w file 写并追加模板块到file末尾。  
W file 写并追加模板块的第一行到file末尾。  
! 表示后面的命令对所有没有被选定的行发生作用。  
= 打印当前行号码。  
# 把注释扩展到下一个换行符以前。  
sed替换标记

g 表示行内全面替换。  
p 表示打印行。  
w 表示把行写入一个文件。  
x 表示互换模板块中的文本和缓冲区中的文本。  
y 表示把一个字符翻译为另外的字符（但是不用于正则表达式）
\1 子串匹配标记
& 已匹配字符串标记
sed元字符集

^ 匹配行开始，如：/^sed/匹配所有以sed开头的行。
$ 匹配行结束，如：/sed$/匹配所有以sed结尾的行。
. 匹配一个非换行符的任意字符，如：/s.d/匹配s后接一个任意字符，最后是d。
* 匹配0个或多个字符，如：/*sed/匹配所有模板是一个或多个空格后紧跟sed的行。
[] 匹配一个指定范围内的字符，如/[ss]ed/匹配sed和Sed。  
[^] 匹配一个不在指定范围内的字符，如：/[^A-RT-Z]ed/匹配不包含A-R和T-Z的一个字母开头，紧跟ed的行。
\(..\) 匹配子串，保存匹配的字符，如s/\(love\)able/\1rs，loveable被替换成lovers。
& 保存搜索字符用来替换其他字符，如s/love/**&**/，love这成**love**。
\< 匹配单词的开始，如:/\<love/匹配包含以love开头的单词的行。
\> 匹配单词的结束，如/love\>/匹配包含以love结尾的单词的行。
x\{m\} 重复字符x，m次，如：/0\{5\}/匹配包含5个0的行。
x\{m,\} 重复字符x，至少m次，如：/0\{5,\}/匹配至少有5个0的行。
x\{m,n\} 重复字符x，至少m次，不多于n次，如：/0\{5,10\}/匹配5~10个0的行。
sed实际用例
#替换oldboy.txt中所有的oldboy变为oldboy_python
#此时结果输出到屏幕,不会写入到文件
sed 's/oldboy/oldboy_python/' /tmp/oldboy.txt
#使用选项-i，匹配每一行第一个oldboy替换为oldboy_python,并写入文件
sed -i 's/oldboy/oldboy_python/' /tmp/oldboy.txt
#使用替换标记g，同样可以替换所有的匹配
sed -i 's/book/books/g' /tmp/oldboy.txt
#删除文件第二行
sed -i '2d' /tmp/oldboy.txt
#删除空白行
sed -i '/^$/d' /tmop/oldboy.txt
#删除文件第二行，到末尾所有行
sed '2,$d' /tmp/oldboy.txt
#显示10-30行
-p --print
-n --取消默认输出
sed -n '10,30p' /tmp/oldboy.txt

sed

```

### scp命令 Linux scp命令用于Linux之间复制文件和目录。
```
Linux scp命令用于Linux之间复制文件和目录。

scp是 secure copy的缩写, scp是linux系统下基于ssh登陆进行安全的远程文件拷贝命令。

语法
scp 【可选参数】 本地源文件 远程文件标记
参数

-r :递归复制整个目录
-v:详细方式输出
-q:不显示传输进度条
-C：允许压缩


实例
#传输本地文件到远程地址
scp 本地文件  远程用户名@远程ip:远程文件夹/
scp 本地文件  远程用户名@远程ip:远程文件夹/远程文件名

scp /tmp/chaoge.py root@192.168.1.155:/home/
scp /tmp/chaoge.py root@192.168.1.155:/home/chaoge_python.py

scp -r  本地文件夹  远程用户名@远程ip:远程文件夹/
scp -r /tmp/oldboy root@192.168.1.155:/home/oldboy

#复制远程文件到本地
scp root@192.168.1.155:/home/oldboy.txt /tmp/oldboy.txt
scp -r root@192.168.1.155:/home/oldboy /home/
```

### 关闭防火墙

#### linux防火墙
```
iptables -L 查看防火墙规则
iptables -F 清空防火墙规则
systemctl stop、start/restart firewalld 关闭firewalld服务，下次启动还会启动
systemctl disable/enable firewalld 永久关闭防火墙开机自启的功能

```

#### 关闭系统自带的，美国航空局的selinux
```
getenforce 获取selinux状态 如果是 Enforcing是开启状态
setenforce 0 临时关闭selinux

```

### history查看历史命令

### du命令 用于显示目录或文件的大小。

Linux du命令用于显示目录或文件的大小。

du会显示指定的目录或文件所占用的磁盘空间。
```
用法
du 【参数】【文件或目录】
-s 显示总计
-h 以k，M,G为单位显示，可读性强
```
实例
```
显示目录或文件所占空间
#什么都不跟，代表显示当前目录所有文件大小
du   

#显示/home的总大小
du -sh /home
```

###　top命令　　用于动态地监视进程活动与系统负载等信息
![image](https://images2018.cnblogs.com/blog/1132884/201807/1132884-20180726152524664-102686644.png)
我们来分析一下图片信息

统计信息区

```
第一行 (uptime)
系统时间 主机运行时间 用户连接数(who) 系统1，5，15分钟的平均负载
第二行:进程信息
进程总数 正在运行的进程数 睡眠的进程数 停止的进程数 僵尸进程数
第三行:cpu信息
1.5 us：用户空间所占CPU百分比
0.9 sy：内核空间占用CPU百分比
0.0 ni：用户进程空间内改变过优先级的进程占用CPU百分比
97.5 id：空闲CPU百分比
0.2 wa：等待输入输出的CPU时间百分比
0.0 hi：硬件CPU中断占用百分比
0.0 si：软中断占用百分比
0.0 st：虚拟机占用百分比

第四行：内存信息（与第五行的信息类似与free命令）
total：物理内存总量
used：已使用的内存总量
free：空闲的内存总量（free+used=total）
buffers：用作内核缓存的内存量

第五行：swap信息
total：交换分区总量
used：已使用的交换分区总量
free：空闲交换区总量
cached Mem：缓冲的交换区总量，内存中的内容被换出到交换区，然后又被换入到内存，但是使用过的交换区没有被覆盖，交换区的这些内容已存在于内存中的交换区的大小，相应的内存再次被换出时可不必再对交换区写入。

```

# 如果服务器有矿机病毒，如何处理？

1. 杀死进程，发现进程杀不死
2. 检查定时任务，杀死定时任务
3. kill -9 强制杀死进程 
4. find / -name sdasdad* 查找有关病毒的文件
5. rm -rf sdasdad* 删除病毒文件

### free -m 查看内存信息

###　chattr命令

给文件加锁，只能写入数据，无法删除文件
```
chattr +a test.py
chattr -a test.py
```

### linux时间同步
linux的date命令可以显示当前时间或者设置系统时间

查看当前时间



格式化输出
```
-d    --date=string    显示指定的时间，而不是当前时间
以年-月-日显示当前时间
date +"%Y-%m-%d"
以年-月-日 时分秒 显示当前时间
date +"%Y-%m-%d %T"
在Linux下系统时间和硬件时间不会自动同步，在Linux运行过程中，系统时间和硬件时间以异步的方式运行，互不干扰。
硬件时间的运行，是靠Bios电池来运行，而系统时间是用CPU tick来维持的。
在系统开机时候，会从Bios中获取硬件时间，设置为系统时间
```

硬件始终的查看
```
[root@oldboy_python ~ 10:19:04]#hwclock
2018年08月27日 星期一 10时23分03秒  -0.528004 秒
```
同步系统时间和硬件时间，可以用hwclock命令
```
//以系统时间为基准，修改硬件时间
[root@oldboy_python ~ 10:29:07]#hwclock -w

//以硬件时间为基准，修改系统时间
[root@oldboy_python ~ 10:29:21]#hwclock -s
```

### 日历命令
cal命令

calendar查看日志的意思 -y 查看一年的日历

```
yugoMBP:~ yuchao$ cal
     March 2019
Su Mo Tu We Th Fr Sa
                1  2
 3  4  5  6  7  8  9
10 11 12 13 14 15 16
17 18 19 20 21 22 23
24 25 26 27 28 29 30
31
```

### Ntp时间服务器

由于我们只需要用作客户端更新时间
```
ntpdate -u ntp.aliyun.com
```

### wget命令

wget命令用于在终端下载网络文件
```
参数是 wget [参数] 下载地址
wget -r -p http://www.luffycity.com#递归下载路飞所有资源，保存到www.luffycity.com文件中

```

### linux文件传到windows上
```
yum install lrzsz -y

sz xxxx.jpg linux文件传到windows上
rz Windows的上传到linux

```

### 开关机命令
```
reboot命令用于重启机器
poweroff用于关闭系统

```