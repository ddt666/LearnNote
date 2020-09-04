[TOC]

## 管道模式 pipeline

> - 普通模式:由于通信会有网络延迟，假如client和server之间的包传输时间需要0.125秒。那么上面的三个命令6个报文至少需要0.75秒才能完成。这样即使redis每秒能处理100个命令，而我们的client也只能一秒钟发出四个命令。这显然没有充分利用 redis的处理能力。

> - 管道模式：（pipeline）可以一次性发送多条命令并在执行完后一次性将结果返回，pipeline通过减少客户端与redis的通信次数来实现降低往返延时时间，而且Pipeline 实现的原理是队列，而队列的原理是时先进先出，这样就保证数据的顺序性。 Pipeline 的默认的同步的个数为53个，也就是说arges中累加到53条数据时会把数据提交。其过程如下图所示：client可以将三个命令放到一个tcp报文一起发送，server则可以将三条命令的处理结果放到一个tcp报文返回。
>     需要注意到是用 pipeline方式打包命令发送，redis必须在处理完所有命令前先缓存起所有命令的处理结果。打包的命令越多，缓存消耗内存也越多。所以并不是打包的命令越多越好。具体多少合适需要根据具体情况测试。

![Redisæ®éæ¨¡å¼ä¸ç®¡éæ¨¡å¼](https://s4.51cto.com/images/blog/201807/06/d3e92160ae35d31521ac6efa46b14bab.png?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_100,g_se,x_10,y_10,shadow_90,type_ZmFuZ3poZW5naGVpdGk=)

```python

import redis

redis_store = redis.StrictRedis(host=127.0.0.1, port=6379, decode_responses=True)

# 创建redis管道对象，可以一次执行多个语句
pipeline = redis_store.pipeline()

# 开启多个语句的记录
pipeline.multi()

pipeline.hset(redis_key, page, resp_json)
pipeline.expire(redis_key, constants.HOUES_LIST_PAGE_REDIS_CACHE_EXPIRES)

# 执行语句
pipeline.execute()
```

