---
title: "[Redis-14]Redis各类型应用"
catalog: true
date: 2021-01-14 19:45:46
subtitle:
header-img:
tags:
- Redis
catagories:
- Redis
---

### String

- 数据缓存功能
- 计数器
- 共享用户Session

### Hash

- 数据缓存功能

### List

> 按插入顺序存储的可重复的集合

- 存储列表结构的数据，比如：粉丝列表、好友列表、文章评论
- 基于lrange的高性能分页
- 简单的消息队列

### Set

> 无序且不可重复的集合

- 基于分布式的全局去重
- 对数据进行交集、差集、并集

### Zset

> 按自然顺序排序且不可重复的集合

- 利用自然排序特性可以实现：排行榜、热搜等
- 实现带权重的队列

### BitMap

> BitMap通过一个bit位来表示某个元素对应的值的状态，其中key就是对应元素本身。BitMap的底层实际上也是对字符串的操作。在Redis 2.2版本之后为BitMap新增了setbit、getbit、bitcount等操作命令，其底层依旧是通过对字符串的操作实现的。

**语法**

setbit key offset value

offset必须是数字，value只能从0或1中取值

```
# 设置2021-04-04的编号为1001的用户为活跃用户
127.0.0.1:6379> setbit 20210404:active 1001 1
127.0.0.1:6379> setbit 20210404:active 1002 1
# 查看1002用户的状态
127.0.0.1:6379> getbit 20210404:active 1002
(integer) 0
# 统计2021-04-04有多少活跃用户
127.0.0.1:6379> bitcount 20210404:active 0 -1
(integer) 2
```

**优点**

- 占用内存非常小，存储一亿个状态位只需要12.5M
- 通过bitcount可以很快速的进行统计，比传统的关系型数据库效率要高很多

### HyperLogLog

> HyperLogLog可以快速的统计数据集的数量，以及将多个数据集进行合并。主要操作命令有pfadd、pfcount、pfmerge

**语法**

```bash
# 记录2020-04-04登录网站的IP
127.0.0.1:6379> pfadd 20210404:login 10.30.11.111
(integer) 1
127.0.0.1:6379> pfadd 20210404:login 10.30.11.112
(integer) 1
127.0.0.1:6379> pfadd 20210404:login 10.30.11.113
(integer) 1
# 统计2020-04-04的登录数
127.0.0.1:6379> pfcount 20210404:login
(integer) 3
# 记录另一个日期登录网站的IP
127.0.0.1:6379> pfadd 20210405:login 10.30.11.113
(integer) 1
127.0.0.1:6379> pfadd 20210405:login 10.30.11.114
(integer) 1
# 合并两天的登录网站的IP
127.0.0.1:6379> pfmerge all 20210404:login 20210405:login
OK
127.0.0.1:6379> pfcount all
(integer) 4
```

**缺点**

只能统计数量，不能查看具体信息

### Geospatial

> 用来保存地理位置，并根据地理位置的经纬度做相关的距离计算。其本质上是借助于zset数据结构来实现的。

**语法**

geoadd key 经度 维度 名称

```bash
# 添加两个城市的位置
127.0.0.1:6379> geoadd cities 116.4 39.9 "beijing" 121.5 31.2 "shanghai"
(integer) 2
# 查询所有城市
127.0.0.1:6379> zrange cities 0 -1
1) "shanghai"
2) "beijing"
# 查询所有城市以及分数
127.0.0.1:6379> zrange cities 0 -1 withscores
1) "shanghai"
2) "4054757618911126"
3) "beijing"
4) "4069885360207904"
# 查询两个城市之间的距离
127.0.0.1:6379> geodist cities beijing shanghai km
"1071.5880"
# 获取城市的坐标
127.0.0.1:6379> geopos cities beijing shanghai
1) 1) "116.39999896287918091"
   2) "39.90000009167092543"
2) 1) "121.49999946355819702"
   2) "31.20000061483705878"
# 计算距离某个坐标点一定范围内的城市
127.0.0.1:6379> georadius cities 120 30 500 km
1) "hangzhou"
2) "shanghai"
# 计算距离某个城市一定范围内的城市
127.0.0.1:6379> georadiusbymember cities shanghai 200 km
1) "hangzhou"
2) "shanghai"
```

