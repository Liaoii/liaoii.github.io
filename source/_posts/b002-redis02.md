---
title: "[Redis-02]安装Redis服务与配置开机启动"
catalog: true
date: 2021-03-18 20:35:47
subtitle: 
header-img:
tags:
- Redis
catagories:
- Redis
---
## 使用install_server.sh安装服务
---
### 安装服务

执行install_server.sh

```bash
cd /opt/redis-5.0.9/utils/
./install_server.sh
```

配置

```bash
Please select the redis port for this instance: [6379] 6380 # 设置端口号
Please select the redis config file name [/etc/redis/6380.conf] # 配置文件位置
Selected default - /etc/redis/6380.conf
Please select the redis log file name [/var/log/redis_6380.log] # 日志文件输出位置
Selected default - /var/log/redis_6380.log
Please select the data directory for this instance [/var/lib/redis/6380] # 存放数据的位置
Selected default - /var/lib/redis/6380
Please select the redis executable path [] /opt/redis/bin/redis-server # 设置redis可执行文件位置    
Selected config:
Port           : 6380
Config file    : /etc/redis/6380.conf
Log file       : /var/log/redis_6380.log
Data dir       : /var/lib/redis/6380
Executable     : /opt/redis/bin/redis-server
Cli Executable : /opt/redis/bin/redis-cli
Is this ok? Then press ENTER to go on or Ctrl-C to abort. # 按Enter确认
Copied /tmp/6380.conf => /etc/init.d/redis_6380 # 服务存放位置
Installing service...
Successfully added to chkconfig!
Successfully added to runlevels 345!
Starting Redis server...
Installation successful!
```

### 修改配置文件

```bash
vim /etc/redis/6380.conf

# 将daemonize由no改为yes，指定redis以守护线程的方式启动
daemonize yes 

# 注释掉bind 127.0.0.1这一行，如果不注释掉该行，将不能在其他机器上进行访问
# bind 127.0.0.1

# 关闭保护模式
protected-mode no 

#指定密码（可选）
requirepass 123456
```

### 相关命令

```bash
service redis_6380 start # 启动redis服务
service redis_6380 stop # 关闭redis服务
service redis_6380 status # 查看redis服务状态
service redis_6380 restart # 重启redis服务
```

这些命令在**/etc/init.d/redis_6380**文件中进行了配置

### 查看日志

```bash
cat /var/log/redis_6380.log
```

## 设置开机自启

---

### 配置service

新增redis_6380.service文件

```bash
cd /etc/systemd/system/
vim redis_6380.service
```

输入如下配置

```bash
[Unit]
Description=Redis server on 6380
After=network.target

[Service]
Type=forking
ExecStart=/etc/init.d/redis_6380 start # 启动
ExecStop=/etc/init.d/redis_6380 stop # 停止
ExecReload=/etc/init.d/redis_6380 restart # 重启
Restart=always

[Install]
WantedBy=multi-user.target
```

### 设置开机自启

```bash
systemctl daemon-reload
systemctl enable redis_6380.service
```

### 重启测试

```bash
reboot # 重启
ps -ef | grep redis # 验证redis是否启动
```

