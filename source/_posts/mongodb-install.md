---
title: mongodb 安装与启动
---
linux安装mongodb
系统版本：CentOS release 6.5
mongodb版本：amazon-3.4.6
下载地址：https://www.mongodb.com/download-center#community 选择需要的版本，复制下载链接。  
- 1.下载压缩包
```
cd ~
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-amazon-3.4.6.tgz
```
- 2.解压
```
tar -zxvf mongodb-linux-x86_64-amazon-3.4.6.tgz
```
- 3.移动到安装目录
```
mv mongodb-linux-x86_64-amazon-3.4.6 mongodb
mv mongodb /usr/local/
```
- 4.配置环境变量
```
vim /etc/profile
最后面加上 export PATH=/usr/local/mongodb/bin:$PATH
```
- 5.新建mongodb默认启动数据库
```
mkdir -p /data/db
```
- 6.启动mongodb
到这已经算安装完成，那么接下来就是把它运行起来。脚本都在mongodb 的bin底下 默认端口
```
cd /usr/local/mongodb/bin
./mongod
```  
>启动参数：--port arg 指定端口 --fork 以守护进程的方式运行MongoDB，创建服务器进程 --auth 开启验证  dbpath = /data/db 数据库存放路径 logpath = /data/db/mongodb.log 日志存放路径 等  

把参数做成配置文件,参数都可以写入 conf/mongod.conf 配置文档里例如：
dbpath = /data/db
logpath = /data/db/mongodb.log
logappend = true
port = 27017
fork = true
auth = true

然后 bin/mongod -f conf/mongo.conf 启动
