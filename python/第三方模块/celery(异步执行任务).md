[TOC]

# 一、celery入门

![celery的结果说明](assets/celery的结果说明.png)



## 1. celery介绍

> Celery是一个功能完备即插即用的任务队列。它使得我们不需要考虑复杂的问题，使用非常简单。celery看起来似乎很庞大，本章节我们先对其进行简单的了解，然后再去学习其他一些高级特性。 celery适用异步处理问题，当发送邮件、或者文件上传, 图像处理等等一些比较耗时的操作，我们可将其异步执行，这样用户不需要等待很久，提高用户体验。 celery的特点是：
>
> - 简单，易于使用和维护，有丰富的文档。
> - 高效，单个celery进程每分钟可以处理数百万个任务。
> - 灵活，celery中几乎每个部分都可以自定义扩展。
>
> celery非常易于集成到一些web开发框架中.



## 2. TaskQueue任务队列

任务队列是一种跨线程、跨机器工作的一种机制.

  任务队列中包含称作任务的工作单元。有专门的工作进程持续不断的监视任务队列，并从中获得新的任务并处理.

  celery通过消息进行通信，通常使用一个叫Broker(中间人)来协client(任务的发出者)和worker(任务的处理者). clients发出消息到队列中，broker将队列中的信息派发给worker来处理。

  一个celery系统可以包含很多的worker和broker，可增强横向扩展性和高可用性能。

<img src="assets/3.png" alt="img" style="zoom: 67%;" />





## 3. celery安装

> 我们可以使用python的包管理器pip来安装:

```python
pip install -U Celery
```

> 也可从官方直接下载安装包:https://pypi.python.org/pypi/celery/

```python
tar xvfz celery-0.0.0.tar.gz
cd celery-0.0.0
python setup.py build
python setup.py install
```

## 4. Broker

> Celery需要一种解决消息的发送和接受的方式，我们把这种用来存储消息的的中间装置叫做message broker, 也可叫做消息中间人。 作为中间人，我们有几种方案可选择：

### 1）RabbitMQ

RabbitMQ是一个功能完备，稳定的并且易于安装的broker. 它是生产环境中最优的选择。使用RabbitMQ的细节参照以下链接：http://docs.celeryproject.org/en/latest/getting-started/brokers/rabbitmq.html#broker-rabbitmq

如果我们使用的是Ubuntu或者Debian发行版的Linux，可以直接通过下面的命令安装RabbitMQ: sudo apt-get install rabbitmq-server 安装完毕之后，RabbitMQ-server服务器就已经在后台运行。如果您用的并不是Ubuntu或Debian, 可以在以下网址： http://www.rabbitmq.com/download.html 去查找自己所需要的版本软件。

### 2）Redis

Redis也是一款功能完备的broker可选项，但是其更可能因意外中断或者电源故障导致数据丢失的情况。 关于是有那个Redis作为Broker，可访下面网址：http://docs.celeryproject.org/en/latest/getting-started/brokers/redis.html#broker-redis

## 5. Application

使用celery第一件要做的最为重要的事情是需要先创建一个Celery实例，我们一般叫做celery应用，或者更简单直接叫做一个app。app应用是我们使用celery所有功能的入口，比如创建任务，管理任务等，在使用celery的时候，app必须能够被其他的模块导入。

### 1）创建应用

我们首先创建tasks.py模块, 其内容为:

```python
from celery import Celery

# 我们这里案例使用redis作为broker
app = Celery('demo', broker='redis://:332572@127.0.0.1/1')

# 创建任务函数
@app.task
def my_task():
    print("任务函数正在执行....")
```

  Celery第一个参数是给其设定一个名字， 第二参数我们设定一个中间人broker, 在这里我们使用Redis作为中间人。my_task函数是我们编写的一个任务函数， 通过加上装饰器app.task, 将其注册到broker的队列中。

  现在我们在创建一个worker， 等待处理队列中的任务.打开终端，cd到tasks.py同级目录中，执行命令:

```
celery -A tasks worker --loglevel=info
```

显示效果如下: 

<img src="assets/4.png" alt="img" style="zoom: 50%;" />

### 2）调用任务

  任务加入到broker队列中，以便刚才我们创建的celery workder服务器能够从队列中取出任务并执行。如何将任务函数加入到队列中，可使用delay()。

进入python终端, 执行如下代码:

```
from tasks import my_task
my_task.delay()
```

执行效果如下:

 <img src="assets/5.png" alt="img" style="zoom: 80%;" />

我们通过worker的控制台，可以看到我们的任务被worker处理。调用一个任务函数，将会返回一个AsyncResult对象，这个对象可以用来检查任务的状态或者获得任务的返回值。

### 3）存储结果

  如果我们想跟踪任务的状态，Celery需要将结果保存到某个地方。有几种保存的方案可选:SQLAlchemy、Django ORM、Memcached、 Redis、RPC (RabbitMQ/AMQP)。

  例子我们仍然使用Redis作为存储结果的方案，任务结果存储配置我们通过Celery的backend参数来设定。我们将tasks模块修改如下:

```python
from celery import Celery

# 我们这里案例使用redis作为broker
app = Celery('demo',
             backend='redis://:332572@127.0.0.1:6379/2',
             broker='redis://:332572@127.0.0.1:6379/1')

# 创建任务函数
@app.task
def my_task(a, b):
    print("任务函数正在执行....")
    return a + b
```

  我们给Celery增加了backend参数，指定redis作为结果存储,并将任务函数修改为两个参数，并且有返回值。 

<img src="assets/6.png" alt="img" style="zoom:80%;" />

更多关于result对象信息，请参阅下列网址:http://docs.celeryproject.org/en/latest/reference/celery.result.html#module-celery.result



## 6、配置

 Celery使用简单，配置也非常简单。Celery有很多配置选项能够使得celery能够符合我们的需要，但是默认的几项配置已经足够应付大多数应用场景了。

  配置信息可以直接在app中设置，或者通过专有的配置模块来配置。

### 1.直接通过app来配置

```python
from celery import Celery
app = Celery('demo')
# 增加配置
app.conf.update(
    result_backend='redis://:332572@127.0.0.1:6379/2',
    broker_url='redis://:332572@127.0.0.1:6379/1',
)
```

### 2.专有配置文件

  对于比较大的项目，我们建议配置信息作为一个单独的模块。我们可以通过调用app的函数来告诉Celery使用我们的配置模块。

  配置模块的名字我们取名为celeryconfig, 这个名字不是固定的，我们可以任意取名，建议这么做。我们必须保证配置模块能够被导入。 配置模块的名字我们取名为celeryconfig, 这个名字不是固定的，我们可以任意取名，建议这么做。我们必须保证配置模块能够被导入。

  下面我们在tasks.py模块 同级目录下创建配置模块celeryconfig.py:

```python
result_backend = 'redis://:332572@127.0.0.1:6379/2'
broker_url = 'redis://:332572@127.0.0.1:6379/1'
```

  tasks.py文件修改为:

```python
from celery import Celery
import celeryconfig

# 我们这里案例使用redis作为broker
app = Celery('demo')

# 从单独的配置模块中加载配置
app.config_from_object('celeryconfig')
```

更多配置:http://docs.celeryproject.org/en/latest/userguide/configuration.html#configuration

## 7、项目中使用celery

 我的项目目录:

TestCelery/ ├── proj │ ├── celeryconfig.py │ ├── celery.py │ ├── **init**.py │ └── tasks.py └── test.py

  celery.py内容如下:

```python
from celery import Celery

# 创建celery实例
app = Celery('demo')
app.config_from_object('proj.celeryconfig')

# 自动搜索任务
app.autodiscover_tasks(['proj'])
```

  celeryconfig.p模块内容如下：

```python
from kombu import Exchange, Queue
BROKER_URL = 'redis://:332572@127.0.0.1:6379/1'
CELERY_RESULT_BACKEND = 'redis://:332572@127.0.0.1:6379/2'
```

  tasks.py模块内容如下:

```python
from proj.celery import app as celery_app

# 创建任务函数
@celery_app.task
def my_task1():
    print("任务函数(my_task1)正在执行....")

@celery_app.task
def my_task2():
    print("任务函数(my_task2)正在执行....")

@celery_app.task
def my_task3():
    print("任务函数(my_task3)正在执行....")
```

  启动worker:

```
celery -A proj worker -l info
```

<img src="assets/8.png" alt="img" style="zoom: 50%;" />

键入ctrl+c可关闭worker.

## 8、调用任务(Calling Task)

调用任务，可使用delay()方法:

```
my_task.delay(2, 2)
```

  也可以使用apply_async()方法，该方法可让我们设置一些任务执行的参数，例如，任务多久之后才执行，任务被发送到那个队列中等等.

```
my_task.apply_async((2, 2), queue='my_queue', countdown=10)
```

任务my_task将会被发送到my_queue队列中，并且在发送10秒之后执行。

  如果我们直接执行任务函数，将会直接执行此函数在当前进程中，并不会向broker发送任何消息。

  无论是delay()还是apply_async()方式都会返回AsyncResult对象，方便跟踪任务执行状态，但需要我们配置result_backend.

  每一个被吊用的任务都会被分配一个ID，我们叫Task ID.



## 9、Designing Work-flows

### 1. signature

  我们到目前为止只是学习了如何使用delay()方法，当然这个方法也是非常常用的。但是有时我们并不想简单的将任务发送到队列中，我们想将一个任务函数(由参数和执行选项组成)作为一个参数传递给另外一个函数中，为了实现此目标，Celery使用一种叫做signatures的东西。

  一个signature包装了一个参数和执行选项的单个任务调用。我们可将这个signature传递给函数。

  我们先看下tasks.py模块中定义的任务函数:

```python
from proj.celery import app as celery_app

# 创建任务函数
@celery_app.task
def my_task1():
    print("任务函数(my_task1)正在执行....")

@celery_app.task
def my_task2():
    print("任务函数(my_task2)正在执行....")

@celery_app.task
def my_task3():
    print("任务函数(my_task3)正在执行....")
```

  我们将my_task1()任务包装称一个signature:

```python
t1 = my_task1.signatures(countdown=10)
t1.delay()
```

### 2. Primitives

  这些primitives本身就是signature对象，因此它们可以以多种方式组合成复杂的工作流程。primitives如下:

  group: 一组任务并行执行，返回一组返回值，并可以按顺序检索返回值。

  chain: 任务一个一个执行，一个执行完将执行return结果传递给下一个任务函数.

  tasks.py模块如下:

```python
from proj.celery import app as celery_app

# 创建任务函数
@celery_app.task
def my_task1(a, b):
    print("任务函数(my_task1)正在执行....")
    return a + b

@celery_app.task
def my_task2(a, b):
    print("任务函数(my_task2)正在执行....")
    return a + b

@celery_app.task
def my_task3(a, b):
    print("任务函数(my_task3)正在执行....")
    return a + b
```

  group案例如下(test.py模块):

```python
from proj.tasks import my_task1
from proj.tasks import my_task2
from proj.tasks import my_task3
from celery import group

# 将多个signature放入同一组中
my_group = group((my_task1.s(10, 10), my_task2.s(20, 20), my_task3.s(30, 30)))
ret = my_group() # 执行组任务
print(ret.get())  # 输出每个任务结果
```

<img src="assets/9.png" alt="img" style="zoom: 80%;" /> 



chain案例如下(test.py模块):

```python
from proj.tasks import my_task1
from proj.tasks import my_task2
from proj.tasks import my_task3
from celery import chain

# 将多个signature组成一个任务链
# my_task1的运行结果将会传递给my_task2
# my_task2的运行结果会传递给my_task3
my_chain = chain(my_task1.s(10, 10) | my_task2.s(20) | my_task3.s(30))
ret = my_chain()  # 执行任务链
print(ret.get())  # 输出最终结果
```

<img src="assets/12.png" alt="img" style="zoom:80%;" />

## 10、Routing

 假如我们有两个worker,一个worker专门用来处理邮件发送任务和图像处理任务，一个worker专门用来处理文件上传任务。

  我们创建两个队列，一个专门用于存储邮件任务队列和图像处理，一个用来存储文件上传任务队列。

  Celery支持AMQP(Advanced Message Queue)所有的路由功能，我们也可以使用简单的路由设置将指定的任务发送到指定的队列中.

  我们需要配置在celeryconfig.py模块中配置 CELERY_ROUTES 项, tasks.py模块修改如下:

```python
from proj.celery import app as celery_app


@celery_app.task
def my_task1(a, b):
    print("my_task1任务正在执行....")
    return a + b


@celery_app.task
def my_task2(a, b):
    print("my_task2任务正在执行....")
    return a + b


@celery_app.task
def my_task3(a, b):
    print("my_task3任务正在执行....")
    return a + b


@celery_app.task
def my_task4(a, b):
    print("my_task3任务正在执行....")
    return a + b


@celery_app.task
def my_task5():
    print("my_task5任务正在执行....")


@celery_app.task
def my_task6():
    print("my_task6任务正在执行....")


@celery_app.task
def my_task7():
    print("my_task7任务正在执行....")
```

  我们通过配置，将send_email和upload_file任务发送到queue1队列中，将image_process发送到queue2队列中。

  我们修改celeryconfig.py:

```python
broker_url='redis://:@127.0.0.1:6379/1'
result_backend='redis://:@127.0.0.1:6379/2'


task_routes=({
    'proj.tasks.my_task5': {'queue': 'queue1'},
    'proj.tasks.my_task6': {'queue': 'queue1'},
    'proj.tasks.my_task7': {'queue': 'queue2'},
    },
)
```

  test.py:

```python
from proj.tasks import *

# 发送任务到路由指定的队列中
```

my_task5.delay() my_task6.delay() my_task7.delay()

~~~bash
&emsp;&emsp;开启两个worker服务器，分别处理两个队列：
```python
celery -A proj worker --loglevel=info -Q queue1
celery -A proj worker --loglevel=info -Q queue2
~~~

  我们同样也可以通过apply_aynsc()方法来设置任务发送到那个队列中:

```python
my_task1.apply_async(queue='queue1')
```

  我们也可设置一个worker服务器处理两个队列中的任务:

```bash
celery -A proj worker --loglevel=info -Q queue1,queue2
```

## 11、定时任务

### 老男孩版：

我们还使用Celery_task这个示例来修改一下
my_celery中进行一下小修改

```

from Celery_task.task_one import one
from Celery_task.task_two import two

# one.delay(10,10)
# two.delay(20,20)

# 定时任务我们不在使用delay这个方法了,delay是立即交给task 去执行
# 现在我们使用apply_async定时执行

#首先我们要先给task一个执行任务的时间
import datetime,time
# 获取当前时间 此时间为东八区时间
ctime = time.time()
# 将当前的东八区时间改为 UTC时间 注意这里一定是UTC时间,没有其他说法
utc_time = datetime.datetime.utcfromtimestamp(ctime)
# 为当前时间增加 10 秒
add_time = datetime.timedelta(seconds=10)
action_time = utc_time + add_time

# action_time 就是当前时间未来10秒之后的时间
#现在我们使用apply_async定时执行
res = one.apply_async(args=(10,10),eta=action_time)
print(res.id)
#这样原本延迟5秒执行的One函数现在就要在10秒钟以后执行了

my_celery

```

![image](https://img2018.cnblogs.com/blog/1122946/201902/1122946-20190210184254479-1240085500.png)


定时任务只能被执行一次,那如果我想每隔10秒都去执行一次这个任务怎么办呢? 周期任务来了

## 12、Celery周期任务

### 老男孩版：

首先要对Celery_task中的celery.py进行一点修改:

```
from celery import Celery
from celery.schedules import crontab

celery_task = Celery("task",
                     broker="redis://127.0.0.1:6379",
                     backend="redis://127.0.0.1:6379",
                     include=["Celery_task.task_one","Celery_task.task_two"])

#我要要对beat任务生产做一个配置,这个配置的意思就是每10秒执行一次Celery_task.task_one任务参数是(10,10)
celery_task.conf.beat_schedule={
    "each10s_task":{
        "task":"Celery_task.task_one.one",
        "schedule":10, # 每10秒钟执行一次
        "args":(10,10)
    },
    "each1m_task": {
        "task": "Celery_task.task_one.one",
        "schedule": crontab(minute=1), # 每一分钟执行一次
        "args": (10, 10)
    },
    "each24hours_task": {
        "task": "Celery_task.task_one.one",
        "schedule": crontab(hour=24), # 每24小时执行一次
        "args": (10, 10)
    }

}

#以上配置完成之后,还有一点非常重要
# 不能直接创建Worker了,因为我们要执行周期任务,所以首先要先有一个任务的生产方
# celery beat -A Celery_task
# celery worker -A Celery_task -l INFO -P eventlet

celery.py

```

创建Worker的方式并没有发行变化,但是这里要注意的是,每间隔一定时间后需要生产出来任务给Worker去执行,这里需要一个生产者beat

celery beat -A Celery_task  #创建生产者 beat 你的 schedule 写在哪里,就要从哪里启动

![image](https://img2018.cnblogs.com/blog/1122946/201902/1122946-20190210190000539-1618431945.png)

celery worker -A Celery_task -l INFO -P eventlet

 创建worker之后,每10秒就会由beat创建一个任务给Worker去执行
 ![image](https://img2018.cnblogs.com/blog/1122946/201902/1122946-20190210190020144-313594421.png)

### 传智播客版：

 celery beat是一个调度器，它可以周期内指定某个worker来执行某个任务。如果我们想周期执行某个任务需要增加beat_schedule配置信息.  

```python
broker_url='redis://:@127.0.0.1:6379/1'
result_backend='redis://:@127.0.0.1:6379/2'

# 指定任务发到那个队列中
task_routes=({
    'proj.tasks.my_task5': {'queue': 'queue1'},
    'proj.tasks.my_task6': {'queue': 'queue1'},
    'proj.tasks.my_task7': {'queue': 'queue2'},
    },
)


# 配置周期性任务，　或者定时任务
beat_schedule = {
    'every-5-seconds':
        {
            'task': 'proj.tasks.my_task8',
            'schedule': 5.0,
            # 'args': (16, 16),
        }
}
```

  tasks.py模块内容如下:

```python
from proj.celery import app as celery_app


@celery_app.task
def my_task1(a, b):
    print("my_task1任务正在执行....")
    return a + b


@celery_app.task
def my_task2(a, b):
    print("my_task2任务正在执行....")
    return a + b


@celery_app.task
def my_task3(a, b):
    print("my_task3任务正在执行....")
    return a + b


@celery_app.task
def my_task4(a, b):
    print("my_task3任务正在执行....")
    return a + b


@celery_app.task
def my_task5():
    print("my_task5任务正在执行....")




@celery_app.task
def my_task6():
    print("my_task6任务正在执行....")



@celery_app.task
def my_task7():
    print("my_task7任务正在执行....")


# 周期执行任务
@celery_app.task
def my_task8():
    print("my_task8任务正在执行....")
```

  启动woker处理周期性任务:

```python
celery -A proj worker --loglevel=info --beat
```

  如果我们想指定在某天某时某分某秒执行某个任务，可以执行cron任务, 增加配置信息如下:

```python
beat_schedule = {
    'every-5-minute':
        {
            'task': 'proj.tasks.period_task',
            'schedule': 5.0,
            'args': (16, 16),
        },
    'add-every-monday-morning': {
        'task': 'proj.tasks.period_task',
        'schedule': crontab(hour=7, minute=30, day_of_week=1),
        'args': (16, 16),
    },

}
```

crontab例子: http://docs.celeryproject.org/en/latest/userguide/periodic-tasks.html

  开启一个celery beat服务：

```
celery -A proj beat
```

  celery需要保存上次任务运行的时间在数据文件中，文件在当前目录下名字叫celerybeat-schedule. beat需要访问此文件：

```
celery -A proj beat -s /home/celery/var/run/celerybeat-schedule
```

# 二、Django使用Celery

## 1. 配置celery

创建django项目celery_demo, 创建应用demo:

```
django-admin startproject celery_demo
python manage.py startapp demo
```

![img](assets/14.png)

  在celery_demo模块中创建celery.py模块, 文件目录为:

![img](assets/15.png)

   celery.py模块内容为:

```python
from celery import Celery
from django.conf import settings
import os

# 为celery设置环境变量
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'celery_demo.settings')

# 创建应用
app = Celery("demo")
# 配置应用
app.conf.update(
    # 配置broker, 这里我们用redis作为broker
    BROKER_URL='redis://:332572@127.0.0.1:6379/1',
)
# 设置app自动加载任务
# 从已经安装的app中查找任务
app.autodiscover_tasks(settings.INSTALLED_APPS)
```

  在应用demo引用创建tasks.py模块, 文件目录为: 

![img](assets/16.png)

  我们在文件内创建一个任务函数my_task:

```python
from celery_demo.celery import app
import time

# 加上app对象的task装饰器
# 此函数为任务函数
@app.task
def my_task():
    print("任务开始执行....")
    time.sleep(5)
    print("任务执行结束....")
```

  在views.py模块中创建视图index:

```python
from django.shortcuts import render
from django.http import HttpResponse
from .tasks import my_task


def index(request):
# 将my_task任务加入到celery队列中
# 如果my_task函数有参数，可通过delay()传递
# 例如 my_task(a, b), my_task.delay(10, 20)
    my_task.delay()

    return HttpResponse("<h1>服务器返回响应内容!</h1>")
```

  在celey_demo/settings.py配置视图路由:

```python
from django.conf.urls import url
from django.contrib import admin
from demo.views import index

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^$', index),
]
```

  创建worker等待处理celery队列中任务, 在终端执行命令:

```
celery -A celery_demo worker -l info
```

![img](assets/17.png) 

启动django测试服务器：

```
python manage.py runserver
```

![img](assets/18.png)

## 2. 存储任务结果

  此处需要用到额外包django_celery_results, 先安装包:

```
pip install django-celery-results
```

  在celery_demo/settings.py中安装此应用:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'demo',
    'django_celery_results',  # 注意此处应用名为下划线
]
```

  回到celery_demo/celery.py模块中，增加配置信息如下:

```python
from celery import Celery
from django.conf import settings
import os

# 为celery设置环境变量
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'celery_demo.settings')

# 创建应用
app = Celery("demo")
# 配置应用
app.conf.update(
    # 配置broker, 这里我们用redis作为broker
    BROKER_URL='redis://:332572@127.0.0.1:6379/1',
    # 使用项目数据库存储任务执行结果
    CELERY_RESULT_BACKEND='django-db',
)
# 设置app自动加载任务
# 从已经安装的app中查找任务
app.autodiscover_tasks(settings.INSTALLED_APPS)
```

  创建django_celery_results应用所需数据库表, 执行迁移文件：

```
python manage.py migrate django_celery_results
```

  我这里使用的是django默认的数据库sqlit, 执行迁移之后，会在数据库中创建一张用来存储任务结果的表: ![img](D:/Program Files/assets/19.png)

![img](assets/19.png)

  再次从浏览器发送请求， 任务执行结束之后，将任务结果保存在数据库中:![img](D:/Program Files/assets/21.png)



## 3. 定时任务

  如果我们想某日某时执行某个任务，或者每隔一段时间执行某个任务，也可以使用celery来完成.   使用定时任务，需要安装额外包:

```
pip install django_celery_beat
```

  首先在settings.py中安装此应用:

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'demo',
    'django_celery_results',
    'django_celery_beat',  # 安装应用
]
```

  在celery_demo/celery.py模块中增加定时任务配置:

```python
from celery import Celery
from django.conf import settings
import os

# 为celery设置环境变量
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'celery_demo.settings')

# 创建应用
app = Celery("demo")
# 配置应用
app.conf.update(
    # 配置broker, 这里我们用redis作为broker
    BROKER_URL='redis://:332572@127.0.0.1:6379/1',
    # 使用项目数据库存储任务执行结果
    CELERY_RESULT_BACKEND='django-db',
    # 配置定时器模块，定时器信息存储在数据库中
    CELERYBEAT_SCHEDULER='django_celery_beat.schedulers.DatabaseScheduler',

)
# 设置app自动加载任务
# 从已经安装的app中查找任务
app.autodiscover_tasks(settings.INSTALLED_APPS)
```

  由于定时器信息存储在数据库中，我们需要先生成对应表, 对diango_celery_beat执行迁移操作，创建对应表:

```
python manage.py migrate django_celery_beat
```

![img](assets/22.png)

  我们可登录网站后台Admin去创建对应任务, 首先我们先在tasks.py模块中增加新的任务，用于定时去执行(5秒执行一次)

```python
from celery_demo.celery import app
import time

# 用于定时执行的任务
@app.task
def interval_task():
    print("我每隔5秒钟时间执行一次....")
```

  首先创建后台管理员帐号:

```
python manage.py createsuperuser
```

  登录管理后台Admin:

![img](assets/23.png)

  其中Crontabs用于定时某个具体时间执行某个任务的时间，Intervals用于每隔多久执行任务的事件，具体任务的执行在Periodic tasks表中创建。

  我们要创建每隔5秒执行某个任务，所以在Intervals表名后面点击Add按钮:

![img](assets/24.png)

  然后在Periodic tasks表名后面，点击Add按钮，添加任务:

![img](assets/25.png)

  启动定时任务：

```
celery -A celery_demo worker -l info --beat
```

![img](assets/26.png)



  任务每隔5秒中就会执行一次，如果配置了存储，那么每次任务执行的结果也会被保存到对应的数据库中。

# 三、windows下使用Celery

> - Windows:这里需要注意的是celery 4.0 已经不再对Windows操作系统提供支持了,
>
> - 也就是在windows环境下出现问题除非自己解决,否贼官方是不会给你解决的 
>
> - eventlet 是一个python的三方库，可以解决兼容问题 

**安装 eventlet** ：

```bash
pip install eventlet
```

**启动celery命令后面加上** `-P eventlet` ：

```bash
 celery worker -A tasks -l INFO -P eventlet 
```

