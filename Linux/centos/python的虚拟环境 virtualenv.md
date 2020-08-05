1.需求在linux服务器上，既可以有django2.0环境，也能运行django1.11.11环境
```
思路：
1.django2.0想要运行，我们得准备python解释器+pip3软件包管理
2.还想运行django1.11.11  python解释器+pip3 
	- 在编译安装一个python3.6???????
	-  pip3安装的模块，都放在/opt/python36/lib/python3.6/site-packages
	
virtualenv 就是一个虚拟解释器
就是基于物理环境下的python解释器，虚拟/分身 出的 多个解释器 

venv1 
	django2.0
venv2
	django1.1
venv3 
	flask
	
venv4 
	requests
	scrapy 
```

### 安装virtualevn

1. 下载virtualenv工具
```
通过物理环境的pip工具安装
pip3 install --upgrade virtualenv==16.7.9

pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple virtualenv
安装完成后你的linux就多了一个virtualenv命令

```
2. 创建虚拟环境venv1  venv2 
```
virtualenv --no-site-packages --python=python3   s15venv1
调用虚拟环境的命令 
--no-site-packages  这是构建干净，隔离的模块的参数 
--python=python3			这个参数是指定虚拟环境以哪一个物理解释器为基础的
最后一个是虚拟环境的名字  会创建这么一个文件夹
```
3. 进入虚拟环境目录，激活虚拟环境
```
	找到你的虚拟环境目录bin地下的activate文件
	source myenv/s15venv1/bin/activate
	-
	激活虚拟环境，原理就是修改了PATH变量，path是有顺序执行的
	echo $PATH 检查环境变量
	which python3 
	which  pip3  检查虚拟环境是否正常
```
4. 测试安装2个虚拟环境，venv1,venv2，并且运行2个django不同版本的项目

5. 退出虚拟换的命令
```
deactivate 
```


### 保证本地开发环境和线上一致性的操作
解决方案：  
1. 通过命令保证环境的一致性，导出当前python环境的包
```
pip3 freeze > requirements.txt   

这将会创建一个 requirements.txt 文件，其中包含了当前环境中所有包及 各自的版本的简单列表。
可以使用 “pip list”在不产生requirements文件的情况下， 查看已安装包的列表。
```

2. 上传至服务器后，在服务器下创建virtualenv，在venv中导入项目所需的模块依赖
```
pip3 install -r requirements.txt
```



### 虚拟环境管理工具virtualenvwrapper

1.安装这个命令，必须得在物理解释器地下，注意！！
```
	pip3 install virtualenvwrapper
	
```
	
1.1
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
 ```
 echo $PATH 
 这里保持配置和我一样，将python3放在最前面
[root@localhost ~]# echo $PATH
/opt/python36/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin:/root/bin
```
	
2.修改环境变量，每次开机就加载这个virtualenvwrapper工具
```
	vim ~/.bashrc   #vim编辑用户家目录下的.bashrc文件，这个文件是用户在登录的时候，就读取这个文件
	#export 是读取shell命令的作用
	#这些变量根据你自己的绝对路径环境修改
	export WORKON_HOME=~/Envs   #设置virtualenv的统一管理目录
	export VIRTUALENVWRAPPER_VIRTUALENV_ARGS='--no-site-packages'   #添加virtualenvwrapper的参数，生成干净隔绝的环境
	export VIRTUALENVWRAPPER_PYTHON=/opt/python347/bin/python3     #指定python解释器
	source /opt/python34/bin/virtualenvwrapper.sh #执行virtualenvwrapper安装脚本 
```	
3.重新登录会话，使得这个配置生效
```
	logout 
	ssh ....
```

4.此时正确的话 virtualenvwrapper工具已经可以使用
提供了哪些命令？
```
lsvirtualenv    列出所有虚拟环境:
 
mkvirtualenv  虚拟环境名   #自动下载虚拟环境，且激活虚拟环境

workon  虚拟环境名   #激活虚拟环境

deactivate  退出虚拟环境 

rmvirtualenv	删除虚拟环境 

cdvirtualenv  进入当前已激活的虚拟环境所在的目录

cdsitepackages 进入当前激活的虚拟环境的，python包的目录

```