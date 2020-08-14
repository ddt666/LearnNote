## ORM [Object Relation Mapping] 对象关系映射
> 大部分web框架都支持 如：django
> Flask-SQLALchemy 基于SQLALchemy

### 常用的SQLAlchemy查询过滤器

|   过滤器    |                       说明                       |
| :---------: | :----------------------------------------------: |
|  filter()   |      把过滤器添加到原查询上，返回一个新查询      |
| filter_by() |    把等值过滤器添加到原查询上，返回一个新查询    |
|    limit    |         使用指定的值限定原查询返回的结果         |
|  offset()   |       偏移原查询返回的结果，返回一个新查询       |
| order_by()  | 根据指定条件对原查询结果进行排序，返回一个新查询 |
| group_by()  | 根据指定条件对原查询结果进行分组，返回一个新查询 |

### 常用的SQLAlchemy查询执行器

|      方法      |                     说明                     |
| :------------: | :------------------------------------------: |
|     all()      |         以列表形式返回查询的所有结果         |
|    first()     |  返回查询的第一个结果，如果未查到，返回None  |
| first_or_404() |  返回查询的第一个结果，如果未查到，返回404   |
|     get()      |   返回指定主键对应的行，如不存在，返回None   |
|  get_or_404()  |   返回指定主键对应的行，如不存在，返回404    |
|    count()     |              返回查询结果的数量              |
|   paginate()   | 返回一个Paginate对象，它包含指定范围内的结果 |

### 1. 创建数据表
```python
# 通过SQLAlchemy创建数据表
# 1.导入SQLAlchemy
from sqlalchemy.ext.declarative import declarative_base

# 2.创建ORM模型基类
Base = declarative_base()  # Django Model

# 3.导入ORM对应数据库数据类型的字段
from sqlalchemy import Column, Integer, String


# 4.创建ORM对象
class User(Base):
    __tablename__ = "user"
    id = Column(Integer, primary_key=True, autoincrement=True)
    name = Column(String(32), index=True)


# 5.创建数据库连接
from sqlalchemy import create_engine

engine = create_engine("mysql+pymysql://root:@127.0.0.1:3306/day127?charset=utf8")
# 数据库连接创建完成

# 6.去数据库中创建与User所对应的数据表
# 去engine数据库中创建所有继承Base类的 ORM对象
Base.metadata.create_all(engine)

# my_create_table.py
```

### 2.增删改查操作

#### 2.1.增加数据
```python
#insert 为数据表增加数据
# insert One 增加一行数据
# insert into user(name) values ("DragonFire")
# 在ORM中的操作:

# 1.首先导入之间做好的ORM 对象 User
from my_create_table import User

# 2.使用Users ORM模型创建一条数据
user1 = User(name="DragonFire")

# 数据已经创建完了,但是需要写入到数据库中啊,怎么写入呢?

# 3.写入数据库:
# 首先打开数据库会话 , 说白了就是创建了一个操纵数据库的窗口
# 导入 sqlalchemy.orm 中的 sessionmaker
from sqlalchemy.orm import sessionmaker

# 导入之前创建好的 create_engine
from my_create_table import engine

# 创建 sessionmaker 会话对象,将数据库引擎 engine 交给 sessionmaker
Session = sessionmaker(engine)

# 打开会话对象 Session
db_session = Session()

# 在db_session会话中添加一条 UserORM模型创建的数据
db_session.add(user1)

# 使用 db_session 会话提交 , 这里的提交是指将db_session中的所有指令一次性提交
db_session.commit()

# 当然也你也可很任性的提交多条数据
# 方法一:
user2 = User(name="Dragon")
user3 = User(name="Fire")
db_session.add(user2)
db_session.add(user3)
db_session.commit()
# 之前说过commit是将db_session中的所有指令一次性提交,现在的db_session中至少有两条指令user2和user3
db_session.close()
#关闭会话

# 如果说你觉得方法一很麻烦,那么方法二一定非常非常适合你
# 方法二:
user_list = [
    User(name="Dragon1"),
    User(name="Dragon2"),
    User(name="Dragon3")
]
db_session.add_all(user_list)
db_session.commit()

db_session.close()

# orm_insert
```


​    
#### 2.2 查询数据

```python
# ORM操作查询数据
# 有了刚才Insert增加数据的经验,那么查询之前的准备工作,就不用再重复了吧
# 回想一下刚才Insert时我们的操作
from my_create_table import User, engine
from sqlalchemy.orm import sessionmaker

Session = sessionmaker(engine)
db_session = Session()

# 1. select * from user 查询user表中的所有数据
# 语法是这样的 使用 db_session 会话 执行User表 query(User) 取出全部数据 all()
user_all_list = db_session.query(User).all()
print(user_all_list)  # [<my_create_table.User object at 0x0000016D7C4BCDD8>]
# 如何查看user_all_list其中的数据呢? 循环呗
for i in user_all_list:
    print(i.id, i.name)  # ORM对象 直接使用调用属性的方法 拿出对应字段的值

db_session.close()
#关闭会话

# 2. select * from user where id >= 20
# 语法是这样的 使用 db_session 会话 执行User表 query(User) 筛选内容User.id >=20 的数据全部取出 all()
user_all_list = db_session.query(User).filter(User.id >= 20).all()
print(user_all_list)
for i in user_all_list:
    print(i.id, i.name)

db_session.close()
#关闭会话

# 3. 除了取出全部还可以只取出一条
user = db_session.query(User).filter(User.id >= 20).first()
print(user.id, user.name)
db_session.close()
#关闭会话

# 4. 乌龙 之 忘了取出数据.......
wulong1 = db_session.query(User).filter(User.id >= 20)
print(wulong1)
#SELECT user.id AS user_id, user.name AS user_name
#FROM user
#WHERE user.id >= %(id_1)s
# Fuck我忘了取出数据了!!!!!!! 哎? wulong1给我显示了原生SQL语句,因祸得福了
wulong2 = db_session.query(User)
print(wulong2)
#SELECT user.id AS user_id, user.name AS user_name
#FROM user
# Fuck我又忘了取出数据了!!!!!!! 哎? wulong2给我显示了原生SQL语句,因祸得福了
db_session.close()
#关闭会话

# orm_select

```

#### 2.3 修改数据

```python
# ORM更新数据
# 无论是更新还是删除,首先要做的事情,就应该是查询吧
# 根据之前原有的经验,接下来是不是要导入ORM对象了,是不是要创建db_session会话了
from my_create_table import User,engine
from sqlalchemy.orm import sessionmaker
Session = sessionmaker(engine)
db_session = Session()

# UPDATE user SET name="NBDragon" WHERE id=20 更新一条数据
# 语法是这样的 :
# 使用 db_session 执行User表 query(User) 筛选 User.id = 20 的数据 filter(User.id == 20)
# 将name字段的值改为NBDragon update({"name":"NBDragon"})
res = db_session.query(User).filter(User.id == 20).update({"name":"NBDragon"})
print(res) # 1 res就是我们当前这句更新语句所更新的行数
# 注意注意注意
# 这里一定要将db_session中的执行语句进行提交,因为你这是要对数据中的数据进行操作
# 数据库中 增 改 删 都是操作,也就是说执行以上三种操作的时候一定要commit
db_session.commit()
db_session.close()
#关闭会话

# 更新多条
res = db_session.query(User).filter(User.id <= 20).update({"name":"NBDragon"})
print(res) # 6 res就是我们当前这句更新语句所更新的行数
db_session.commit()
db_session.close()
#关闭会话

# orm_update.py

```

#### 2.4 删除数据

```python
# ORM 删除一条多条数据
# 老规矩
# 导入 ORM 创建会话
from my_create_table import User,engine
from sqlalchemy.orm import sessionmaker
Session = sessionmaker(engine)
db_session = Session()

# DELETE FROM `user` WHERE id=20
res = db_session.query(User).filter(User.id==20).delete()
print(res)
# 是删除操作吧,没错吧,那你想什么呢?commit吧
db_session.commit()

db_session.close()
#关闭会话

# orm_delete.py

```

#### 2.5 高级版查询操作

```python
# 高级版查询操作,厉害了哦
#老规矩
from my_create_table import User,engine
from sqlalchemy.orm import sessionmaker

Session = sessionmaker(engine)
db_session = Session()

# 查询数据表操作
# and or
from sqlalchemy.sql import and_ , or_
ret = db_session.query(User).filter(and_(User.id > 3, User.name == 'DragonFire')).all()
ret = db_session.query(User).filter(or_(User.id < 2, User.name == 'DragonFire')).all()

# 查询所有数据
r1 = db_session.query(User).all()

# 查询数据 指定查询数据列 加入别名
r2 = db_session.query(User.name.label('username'), User.id).first()
print(r2.id,r2.username) # 15 NBDragon

# 表达式筛选条件
r3 = db_session.query(User).filter(User.name == "DragonFire").all()

# 原生SQL筛选条件
r4 = db_session.query(User).filter_by(name='DragonFire').all()
r5 = db_session.query(User).filter_by(name='DragonFire').first()

# 字符串匹配方式筛选条件 并使用 order_by进行排序
r6 = db_session.query(User).filter(text("id<:value and name=:name")).params(value=224, name='DragonFire').order_by(User.id).all()

#原生SQL查询
r7 = db_session.query(User).from_statement(text("SELECT * FROM User where name=:name")).params(name='DragonFire').all()

# 筛选查询列
# query的时候我们不在使用User ORM对象,而是使用User.name来对内容进行选取
user_list = db_session.query(User.name).all()
print(user_list)
for row in user_list:
    print(row.name)

# 别名映射  name as nick
user_list = db_session.query(User.name.label("nick")).all()
print(user_list)
for row in user_list:
    print(row.nick) # 这里要写别名了

# 筛选条件格式
user_list = db_session.query(User).filter(User.name == "DragonFire").all()
user_list = db_session.query(User).filter(User.name == "DragonFire").first()
user_list = db_session.query(User).filter_by(name="DragonFire").first()
for row in user_list:
    print(row.nick)

# 复杂查询
from sqlalchemy.sql import text
user_list = db_session.query(User).filter(text("id<:value and name=:name")).params(value=3,name="DragonFire")

# 查询语句
from sqlalchemy.sql import text
user_list = db_session.query(User).filter(text("select * from User id<:value and name=:name")).params(value=3,name="DragonFire")

# 排序 :
user_list = db_session.query(User).order_by(User.id).all()
user_list = db_session.query(User).order_by(User.id.desc()).all()
for row in user_list:
    print(row.name,row.id)

# offset 偏移，跳过n条
user_list = db_session.query(User).offset(2).all()

# limit 限制取n条
user_list = db_session.query(User).offset(1).limit(2).all() #跳过1条取两条
    
#其他查询条件
"""
ret = session.query(User).filter_by(name='DragonFire').all()
ret = session.query(User).filter(User.id > 1, User.name == 'DragonFire').all()
ret = session.query(User).filter(User.id.between(1, 3), User.name == 'DragonFire').all() # between 大于1小于3的
ret = session.query(User).filter(User.id.in_([1,3,4])).all() # in_([1,3,4]) 只查询id等于1,3,4的
ret = session.query(User).filter(~User.id.in_([1,3,4])).all() # ~xxxx.in_([1,3,4]) 查询不等于1,3,4的
ret = session.query(User).filter(User.id.in_(session.query(User.id).filter_by(name='DragonFire'))).all() 子查询
from sqlalchemy import and_, or_
ret = session.query(User).filter(and_(User.id > 3, User.name == 'DragonFire')).all()
ret = session.query(User).filter(or_(User.id < 2, User.name == 'DragonFire')).all()
ret = session.query(User).filter(
    or_(
        User.id < 2,
        and_(User.name == 'eric', User.id > 3),
        User.extra != ""
    )).all()
# select * from User where id<2 or (name="eric" and id>3) or extra != "" 

# 通配符
ret = db_session.query(User).filter(User.name.like('e%')).all()
ret = db_session.query(User).filter(~User.name.like('e%')).all()

# 限制
ret = db_session.query(User)[1:2]

# 排序
ret = db_session.query(User).order_by(User.name.desc()).all()
ret = db_session.query(User).order_by(User.name.desc(), User.id.asc()).all()

# 分组
from sqlalchemy.sql import func

ret = db_session.query(User.role_id, func.count(User.role_id)).group_by(User.role_id).all()

ret = db_session.query(User).group_by(User.extra).all()
ret = db_session.query(
    func.max(User.id),
    func.sum(User.id),
    func.min(User.id)).group_by(User.name).all()

ret = db_session.query(
    func.max(User.id),
    func.sum(User.id),
    func.min(User.id)).group_by(User.name).having(func.min(User.id) >2).all()
"""

# 关闭连接
db_session.close()

# orm_select_more.py

```

#### 2.6.高级修改数据操作

```python
#高级版更新操作
from my_create_table import User,engine
from sqlalchemy.orm import sessionmaker

Session = sessionmaker(engine)
db_session = Session()

#直接修改
db_session.query(User).filter(User.id > 0).update({"name" : "099"})

#在原有值基础上添加 - 1
db_session.query(User).filter(User.id > 0).update({User.name: User.name + "099"}, synchronize_session=False)

#在原有值基础上添加 - 2
db_session.query(User).filter(User.id > 0).update({"age": User.age + 1}, synchronize_session="evaluate")
db_session.commit()

# orm_update_more.py

```

### 3.一对多的操作 : ForeignKey


#### 3.1.创建数据表及关系relationship:

```python
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

# 这次我们要多导入一个 ForeignKey 字段了,外键关联对了
from sqlalchemy import Column,Integer,String,ForeignKey
# 还要从orm 中导入一个 relationship 关系映射
from sqlalchemy.orm import relationship

class ClassTable(Base):
    __tablename__="classtable"
    id = Column(Integer,primary_key=True)
    name = Column(String(32),index=True)

class Student(Base):
    __tablename__="student"
    id=Column(Integer,primary_key=True)
    name = Column(String(32),index=True)

    # 关联字段,让class_id 与 class 的 id 进行关联,主外键关系(这里的ForeignKey一定要是表名.id不是对象名)
    class_id = Column(Integer,ForeignKey("classtable.id"))

    # 将student 与 classtable 创建关系 这个不是字段,只是关系,backref是反向关联的关键字
    to_class = relationship("ClassTable",backref = "stu2class")

from sqlalchemy import create_engine
engine = create_engine("mysql+pymysql://root:DragonFire@127.0.0.1:3306/dragon?charset=utf8")

Base.metadata.create_all(engine)

my_ForeignKey.py
```

### 3.2.基于relationship增加数据

```python
from my_ForeignKey import Student, ClassTable,engine
# 创建连接
from sqlalchemy.orm import sessionmaker
# 创建数据表操作对象 sessionmaker
DB_session = sessionmaker(engine)
db_session = DB_session()

# 增加数据
# 1.简单增加数据
# 添加两个班级:
# db_session.add_all([
#     ClassTable(name="OldBoyS1"),
#     ClassTable(name="OldBoyS2")
# ])
# db_session.commit()
# 添加一个学生 DragonFire 班级是 OldBoyS1
# 查询要添加到的班级
# class_obj = db_session.query(ClassTable).filter(ClassTable.name == "OldBoyS1").first()
# 创建学生
# stu = Student(name="DragonFire",class_id = class_obj.id)
# db_session.add(stu)
# db_session.commit()

# 2. relationship版 添加数据
# 通过关系列 to_class 可以做到两件事
# 第一件事 在ClassTable表中添加一条数据
# 第二件事 在Student表中添加一条数据并将刚刚添加的ClassTable的数据id填写在Student的class_id中
# stu_cla = Student(name="DragonFire",to_class=ClassTable(name="OldBoyS1"))
# print(stu_cla.name,stu_cla.class_id)
# db_session.add(stu_cla)
# db_session.commit()

# 3.relationship版 反向添加数据
# 首先建立ClassTable数据
class_obj = ClassTable(name="OldBoyS2")
# 通过class_obj中的反向关联字段backref - stu2class
# 向 Student 数据表中添加 2条数据 并将 2条数据的class_id 写成 class_obj的id
# class_obj.stu2class = [Student(name="BMW"),Student(name="Audi")]
# db_session.add(class_obj)
# db_session.commit()

# 关闭连接
db_session.close()

orm_ForeignKey_insert.py

```

#### 3.3.基于relationship查询数据
```python
from my_ForeignKey import Student, ClassTable,engine

from sqlalchemy.orm import sessionmaker
DB_session = sessionmaker(engine)
db_session = DB_session()

# 1.查询所有数据,并显示班级名称,连表查询
student_list = db_session.query(Student).all()
for row in student_list:
    # row.to_class.name 通过Student对象中的关系字段relationship to_class 获取关联 ClassTable中的name
    print(row.name,row.to_class.name,row.class_id)

# 2.反向查询
class_list = db_session.query(ClassTable).all()
for row in class_list:
    for row2 in row.stu2class:
        print(row.name,row2.name)
# row.stu2class 通过 backref 中的 stu2class 反向关联到 Student 表中根据ID获取name


db_session.close()

orm_ForeignKey_select.py
```
#### 3.4.更新数据
```python
from my_ForeignKey import Student, ClassTable,engine

from sqlalchemy.orm import sessionmaker
DB_session = sessionmaker(engine)
db_session = DB_session()

# 更新
class_info = db_session.query(ClassTable).filter(ClassTable.name=="OldBoyS1").first()
db_session.query(Student).filter(Student.class_id == class_info.id).update({"name":"NBDragon"})
db_session.commit()

db_session.close()

orm_ForeignKey_update
```
#### 3.5.删除数据
```python
from my_ForeignKey import Student, ClassTable,engine

from sqlalchemy.orm import sessionmaker
DB_session = sessionmaker(engine)
db_session = DB_session()

# 删除
class_info = db_session.query(ClassTable).filter(ClassTable.name=="OldBoyS1").first()
db_session.query(Student).filter(Student.class_id == class_info.id).delete()
db_session.commit()

db_session.close()

orm_ForeignKey_delete.py
```

### 4.多对多 : ManyToMany

#### 4.1.创建表及关系

```python
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()

from sqlalchemy import Column,Integer,String,ForeignKey
from sqlalchemy.orm import relationship

class Hotel(Base):
    __tablename__="hotel"
    id=Column(Integer,primary_key=True)
    girl_id = Column(Integer,ForeignKey("girl.id"))
    boy_id = Column(Integer,ForeignKey("boy.id"))

class Girl(Base):
    __tablename__="girl"
    id=Column(Integer,primary_key=True)
    name = Column(String(32),index=True)

    #创建关系
    boys = relationship("Boy",secondary="hotel",backref="girl2boy")


class Boy(Base):
    __tablename__="boy"
    id=Column(Integer,primary_key=True)
    name = Column(String(32),index=True)


from sqlalchemy import create_engine
engine = create_engine("mysql+pymysql://root:DragonFire@127.0.0.1:3306/dragon?charset=utf8")

Base.metadata.create_all(engine)

my_M2M.py

```
#### 4.2.基于relationship增加数据
```python
from my_M2M import Girl,Boy,Hotel,engine

# 创建连接
from sqlalchemy.orm import sessionmaker
# 创建数据表操作对象 sessionmaker
DB_session = sessionmaker(engine)
db_session = DB_session()

# 1.通过Boy添加Girl和Hotel数据
boy = Boy(name="DragonFire")
boy.girl2boy = [Girl(name="赵丽颖"),Girl(name="Angelababy")]
db_session.add(boy)
db_session.commit()

# 2.通过Girl添加Boy和Hotel数据
girl = Girl(name="珊珊")
girl.boys = [Boy(name="Dragon")]
db_session.add(girl)
db_session.commit()

orm_M2M_insert.py
```

#### 4.3.基于relationship查询数据
```python
from my_M2M import Girl,Boy,Hotel,engine

# 创建连接
from sqlalchemy.orm import sessionmaker
# 创建数据表操作对象 sessionmaker
DB_session = sessionmaker(engine)
db_session = DB_session()

# 1.通过Boy查询约会过的所有Girl
hotel = db_session.query(Boy).all()
for row in hotel:
    for row2 in row.girl2boy:
        print(row.name,row2.name)

# 2.通过Girl查询约会过的所有Boy
hotel = db_session.query(Girl).all()
for row in hotel:
    for row2 in row.boys:
        print(row.name,row2.name)

orm_M2M_select.py
```