---
title: "[Redis-03]Redis常用命令"
catalog: true
date: 2021-03-20 15:20:08
subtitle: 
header-img:
tags:
- Redis
catagories:
- Redis
---

> [官方命令大全](http://www.redis.cn/commands.html) 

### 通用命令

```bash
keys pattern # 查询满足条件的所有key
del key # 删除一个key
exists key # 查看某个key是否存在
expire key seconds # 为某一个key指定生存时间
ttl key # 查看剩余生存时间
persist key # 清楚生存时间
pexpire key milliseconds # 设置毫秒级的生存时间
rename key newkey # 重命名一个key
type key # 查看key的数据类型
```

### string

```bash
set key value # 将键设定为指定的值
get key # 获取一个键的值
getset key value # 获取并设置一个键的值
incr key # 使一个键的值增加1（该键的值类型必须为整型）
incrby key increment # 使一个键增加指定的数
decr key # 使一个键的值减少1
decrby key decrement # 使一个键的值减少指定的数
append key value # 在一个键的值后面拼接字符串
strlen key # 查看一个键的值的长度
mset key value [key value ...] # 同时设置多个键的值
mget key [key ...] # 同时获取多个键的值
setnx key value # 只有当key不存在时才进行设置值，不会覆盖该key的值
```

### hash

key指代对象，field指代对象的字段

```bash
hset key field value # 设置对象的一个字段的值
hget key field # 获取对象某个字段的值
hdel key filed [filed ...] # 删除对象的一个或多个字段
hmset key field value [field value ...] # 同时设置对象的多个字段的值
hmget key field [field ...] # 同时获取对象的多个字段的值
hgetall key # 获取对象所有的字段及值
hincrby key field increment # 为对象的某个字段增加指定的数值（该字段为整型）
hexists key field # 判断字段是否存在
hkeys key # 获取对象的所有字段名
hvals key # 获取对象所有字段的值
hlen key # 获取字段的数量
hsetnx key field value # 当对象的该字段不存在时才新增字段与值


```

### list

list是一个按插入顺序存储的可重复的集合，底层是一个双向链表

```bash
lpush key value [value ...] # 从列表的左边依次插入一个或多个值
rpush key value [value ...] # 从列表的右边依次插入一个或多个值
lpop key # 从列表的左边移除并返回一个元素
rpop key # 从列表的右边移除并返回一个元素
llen key # 获取列表中元素的个数
lrange key start end # 获取列表中从start到end索引的一个片段，其中-1代表最后一个元素
lrem key count value # 删除列表中指定个数的某个值，count为正时从列表的左边开始，为负表示从列表的右边开始
lindex key index # 获取列表中指定索引的元素值
lset key index value # 设置列表中指定索引的元素值
ltrim key start end # 从列表中截取一个片段
linsert key before/after pivot value # 从列表中检索值为pivot的索引，并在其前面或后面插入元素
rpoplpush source destination # 从source中移除最后一个元素并放到destination的列表头部
```

### set

set是一个无序且不可重复的集合

```bash
sadd key member [member ...] # 向一个集合中添加一个或多个元素
srem key member [member ...] # 从一个集合中删除一个或多个元素
smembers key # 获取集合中的所有元素
sismember key member # 判断一个元素是否在集合中
sdiff key [key ...] # 一个集合与其他集合元素的差集
sinter key [key ...] # 一个集合与其他集合元素的交集
sunion key [key ...] # 一个集合与其他集合元素的并集
scard key # 获取集合中元素的个数
spop key # 从集合中随机弹出一个元素
```

### zset

zset是按自然排序且不可重复的集合

```bash
zadd key score member [score member ...] # 向一个集合添加多个元素以及元素的分数
zrange key start end [withscores] # 按照分数从小到大排序后，返回一定排名范围内的元素
zrevrange key start end [withscores] # 按照分数从大到小排序后，返回一定排名范围内的元素
zscore key member # 获取集合中某个元素的分数
zrem key member [member ...] # 删除集合中的一个或多个元素
zrangebyscore key min max [withscores] # 获取集合中某个分数段的元素
zincrby key increment member # 为集合中某个元素增加指定的分数
zcard key # 获取集合中元素的数量
zcount key min max # 获取指定分数范围内的元素个数
zremrangebyrank key start end # 按照排名删除一定范围内的元素
zremrangebyscore key min max # 按照排名删除一定分数范围内的元素
zrank key member # 按从小到大排序后获取某个元素的排名
zrevrank key member # 按从大到小排序后获取某个元素的排名
```

