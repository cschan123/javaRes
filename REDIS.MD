# REDIS

### 1、基础知识

默认16个数据库

key * 查看数据库所有key

flushdb 清楚当前数据库

flushall 清楚所有数据库

默认端口6379

redis是单线程

redisde的瓶颈是根据机器的内存和网络带宽决定的

redis c语言写的，提供数据100000的QPS，完全不比key-value de Memecache差

为什么redis单线程快？

- 核心redis 将所有数据全部放在内存中，所以说使用单线程操作效率最高，多线程还涉及到cpu上下文切换，内存系统，如果没有没有上下文切换效率就是最高的，多次读写都是在同一个CPU上，在内存情况

#### 五大数据类型

Redis-Key

Expire 过期

type name 查看当前key的类型

type key 

- String字符串
  - set key1 v1 //设置key value
  - get key1 //获取 key
  - keys * //查看所有key
  - exisits key1 //判断key 是否存在
  - append key "hello" //追加
  - get key   //v1hello 获取key
  - strlen key // 查询长度
  - clear //清除flushdb
  - 场景为阅读量：
    - set views 0 // 设置浏览量
    - get views //查看浏览量
    - incr views //加1
    - get views //查看浏览量
    - decr views // -1
    - incrby view 10//每次加10 步长
    - decrby view 5 //每次-5
  - 字符串：
    - set key1 "hello ccs"
    - getrange key1 0 3 //截取【0,3】
    - set key2 abcd //替换指定位置开始的字符串
    - setrange key2 1 xx   //abcd->axxd
  - setex
  - setnx  
  - mset k1 v1 k2 v2 k3 v3 //多次设置key value//具有原子性
  - mget k1 k2 k3 //获取多个值
  - msetnx 同时设置多个值，有一个不成功都不成功（原子性）
- List列表
  - Lpush list one 放在列表头部（左边）
  - Lpush list two
  - Lpush list three
  - Lrange list 0 -1//获取list全部值
  - Rpush list four  // 放在列表尾部（右边）
  - Lpop   // 移除列表的头部的值
  - Rpop   // 移除列表的尾部的值
  - Lindex  // 通过下标获取值
  - Llen // list的长度
  - Lrem //移除指定值
  - Ltrim  list 0 1截断指定位置
- Set(集合)不能重复
  - sadd myset "ccs" 添加ccs
  - sadd  myset "hello" //添加myset
  - smembers myset 查看myset
  - sismember  myset hello 判断某个值在不在set中
  - srem 移除
  - scard 获取set集合的内容元素个数
- Hash(哈希)
  - map集合 key-map（filed1 ccs）
  - hset myhash filed1 ccs
  - hget myhash filed1 返回"ccs"
  - hmset 设置多个
  - hgetall myhash 获取所有hash
  - hdel myhash filed1删除指定key
  - hlen 长度 
  - hexists 判断hash中指定字段是哦否存在
- Zset(有序集合)
  - 在set基础上增加sort
  - zadd salary 2500 xiaohong
  - zadd salary 500 zhangsan
  - zadd salary 5000 xiaochen
  - zrangebyscore salary -ing  +inf 从小到大排序

#### 三种特殊数据类型

**geospatial地理位置**

朋友的定位，附近的人，打车距离计算

redis ->geo

参数 key 值（维度 经度 名称）

geoadd china:city   39.90 116.40 beijing

geoadd china:city 31.23 121.47  shanghai

geoadd china:city 29.53 106.50  chongqing

geodist china:city 30.24 12.16  hangzhou 34.26 108.96  xian

geopos china :city beijing shanghai 获取指定城市经纬度

geodist china:key beijing shanghai km 获取直线距离

附近的人

georadius 城市为中心，区域为中心

georadius china:city 110 30 500km （withdist/withcoord） 以110,30经纬度为中心的500km为半径的范围

**Hyperloglog 基数统计的算法**

场景：网页的uv，用户访问量 ，访问多次，还是算一个人

pfadd mykey a b c d  创建第一组元素

pdadd mykey d e f g 创建第二组元素

pfcount 计数

pfmerge 合并

**bitmaps 位存储**

统计用户信息，活跃，登录，打卡，两个状态都可以用bitmaps

bitmaps位图，数据结构，二进制记录，只有01两个状态

```bash
实例讲解：
setbit sign 0 1 星期一 打卡
setbit sign 1 0 星期二 没打卡
setbit sign 2 1 星期三 打卡
setbit sign 3 1 星期四 打卡
setbit sign 4 0 星期五 没打卡
统计哪天没打卡
```

### 事务

redis没有隔离级别

所有命令没有被直接执行，至于发起命令才会执行

redis单条命令保存原子性，但是事务不保证原子性

redis的事务

- 开启事务（multi）
- 命令入队（）setget等命令
- 执行事务（exec）
- 取消事务（discard）

||

乐观锁：认为不会出现问题，不会上锁，更新数据判断一下，是否有人更改数据 version

获取version

更新的时候比较version

悲观锁：认为啥时候都会出现问题，都会上锁

```bash
set money 100 
set out 0
watch money 监视money （如果别的线程操作Money,这个事务执行会失败）
multi 开启事务
decrby money 20 
incrby out 20
exec //执行事务
```



### Jedis 

java 操作redis

jedis是redis官方推荐的java连接开发工具，使用java操作redis中间件，主要学习jedis

idea 新建项目，导入redis-01-jedis         《mvnres》

1、test jedis ping

Jedis jedis = new jedis（”127.0.0.1“,6379）

sout(jedis.ping())

### Springboot整合

SpringBoot操作数据：Spring-data,jpa,jdbc,mongdb ,redis

SpringData和SpringBoot齐名

说明Springboot 2.X之后原来的jedis被替换为 lettuce

jedis：采用直连，多个线程操作的话，是不安全的，如果想要避免不安全，使用jedis pool 连接池，像BIO模式

lettuce：采用netty，实例可以在多个线程中进行共享，不存在线程不安全的情况，可以减少线程数据，像NIO模式