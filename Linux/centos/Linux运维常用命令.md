## linux一个程序运行后
```
!ps
ps -ef|grep  查看任务是否运行有进程
netstat -tunlp | grep  查看任务的端口是否启动
```
### 杀进程
```
kill pid 杀死进程
kill -9 pid 强制杀死进程
pkill redis-server 根据服务名杀死进程
```

## 过滤出文件的空白行和注释行
```
grep -v "^#"  redis.conf |   grep  -v "^$"

```

### 替换文件中字符串并存入另一个文件中
```
sed "s/6379/6380/g" redis-6379.conf > redis-6380.conf 
```

### 批量递归创建文件夹

```
mkdir -p /data/{6379,6380}

```

### 查看网站的响应头信息
```
curl -I  网站域名  	可以		查看网站用了什么服务器
```

### 实时监控某个文件

```
tail -f assce.log
```

### 网站压力测试工具
```
ab
```

### 切换路径后，返回切换前的路径
```
cd -


```

###在ssh断开时，让程序继续不中断运行

```
nohup command &
```
