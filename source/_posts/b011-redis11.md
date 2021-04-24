---
title: "[Redis-11]Redis主从复制"
catalog: true
date: 2021-01-11 13:13:46
subtitle:
header-img:
tags:
- Redis
catagories:
- Redis
---

## 什么是主从复制

Redis的持久化保证了Redis在服务重启时不会丢失数据，因为Redis服务重启后会将硬盘上持久化的数据恢复到内存中。既然Redis的数据是持久化到硬盘的，如果Redis服务器的硬盘损坏，这时就可能导致数据的丢失。通过Redis的主从复制机制就可以避免这种单点故障问题。

如图所示：

![主从复制](master-slave.png)

说明：

- 此时的Redis由多个Redis服务组成，其中一个作为主Redis，其他作为从Redis。主Redis中的数据和从Redis中的数据会保持实时同步，当主Redis中写入数据时会通过主从复制机制将数据复制到其他从Redis上。
- 在主从复制架构中，即使主Redis宕机了，其他的从Redis也能继续提供服务
- 只能有一个主Redis，可以有多个从Redis，且数据只能从主Redis向从Redis复制
- 主从复制不会阻塞主Redis，在同步数据时，主Redis可以继续处理客户端请求
- 一个Redis可以既是主Redis又是从Redis

## 实现原理

> Redis的主从同步分为全量同步和增量同步，当从机第一次连接上主机时需要进行全量同步。从机断线重连后主机会判断从机的runid是否一致，如果一致则进行增量同步，不一致则进行全量同步。除此之外的情况都是增量同步。

其判断流程如下：

![流程](flow.png)

### **全量同步**

全量同步过程主要分三个阶段：

- 同步快照阶段：Master创建并发送快照给Slave，Slave载入并解析快照。Master将该阶段产生的新的写命令存储到缓冲区。
- 同步写缓冲阶段：Master向Slave同步存储在缓冲区的写操作命令
- 同步增量阶段：Master向Slave同步写操作命令

### 增量同步

Redis增量同步主要指slave完成初始化后开始正常工作时，Master发生的写操作同步到Slave的过程。通常情况下，Master每执行一个写命令就会向Slave发送相同的写命令，然后Slave接收并执行

## 主从配置

修改从Redis中的redis.conf配置文件

```
# 在配置中找到该处，指定主服务器的IP地址和端口号
# replicaof <masterip> <masterport>
# Redis 4.0版本之前使用slaveof
slaveof 127.0.0.1 6379
# Redis 4.0版本之后默认为replicaof，但两种配置都可以起作用
replicaof 127.0.0.1 6379
```

