---
title: "[Redis-01]单机安装"
catalog: true
date: 2021-03-18 20:35:47
subtitle: "CentOS7下安装Redis单机版"
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
启动页面如下：
![redis start](start.png)

可以使用Ctrl+C停止当前服务

使用前端启动的缺点是一旦当前命令行窗口被关闭，redis服务将自动停止

### 后端启动
拷贝并修改redis.conf配置文件：
```bash
cp /opt/redis-5.0.9/redis.conf /opt/redis/bin/
vim /opt/redis/bin/redis.conf
# 修改如下

# 将daemonize由no改为yes，指定redis以守护线程的方式启动
daemonize yes 

# 注释掉bind 127.0.0.1这一行，如果不注释掉该行，将不能在其他机器上进行访问
# bind 127.0.0.1

# 关闭保护模式
protected-mode no 
```
启动：
```bash
cd /opt/redis/bin
./redis-server redis.conf
```
检查运行状态：
```bash
ps -ef | grep redis
```
关闭：
```bash
cd /opt/redis/bin
./redis-cli shutdown
```
## 设置密码

```bash
vim /opt/redis/bin/redis.conf
# 找到requirepass，取消注释，设置密码
requirepass "<your password>"
```

## 连接测试

---
```bash
cd /opt/redis/bin
./redis-cli -h 127.0.0.1 -p 6379
# 或者使用如下命令
./redis-cli
```