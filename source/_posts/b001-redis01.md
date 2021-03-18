---
title: Redis单机安装
catalog: true
date: 2021-03-18 20:35:47
subtitle:
header-img:
tags:
- Redis
catagories:
- Redis
---

## 安装

---

### 安装gcc与wget

```bash
yum install -y gcc-c++
yum install -y wget
```

### 下载源码包

进入[Redis](https://download.redis.io/releases/?_ga=2.188597224.1988752644.1616078086-1814083321.1616078086)下载页，在需要下载的版本连接上点击右键，复制链接地址

我选择的是**redis-5.0.9.tar.gz**，地址为 https://download.redis.io/releases/redis-5.0.9.tar.gz

```bash
cd /opt #可根据自身需要进行选择
wget https://download.redis.io/releases/redis-5.0.9.tar.gz
```

### 解压

```bash
tar -zxf redis-5.0.9.tar.gz
```

### 编译&安装

```bash
cd redis-5.0.9
make
make install PREFIX=/opt/redis
```

## 启动

---

### 前端启动

```bash
cd /opt/redis/bin
./redis-server
```

![](E:\blog\source\_posts\b001-redis01\start.png)

可以使用Ctrl+C停止当前服务

使用前端启动的缺点是一旦当前命令行窗口被关闭，redis服务将自动停止