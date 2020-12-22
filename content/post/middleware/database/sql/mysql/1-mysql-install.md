---
title: "1.MySQL编译安装过程"
date: 2019-11-22T19:36:27+08:00
lastmod: 2019-11-22T19:36:27+08:00
keywords: ['middleware']
description: ""
tags: ['complite','MySQL']
categories: ['middleware']
author: ""
---
# 编译安装过程

### 1.预编译
```shell
cmake . -DCMAKE_INSTALL_PREFIX=/data/ops/mysql/ \
-DMYSQL_DATADIR=/data/ops/mysql/data \
-DWITH_BOOST=../boost_1_59_0 \
-DSYSCONFDIR=/etc \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_PARTITION_STORAGE_ENGINE=1 \
-DWITH_FEDERATED_STORAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DWITH_MYISAM_STORAGE_ENGINE=1 \
-DENABLED_LOCAL_INFILE=1 \
-DENABLE_DTRACE=0 \
-DDEFAULT_CHARSET=utf8mb4 \
-DDEFAULT_COLLATION=utf8mb4_general_ci \
-DWITH_EMBEDDED_SERVER=1
```
### 2.编译安装
```shell
make -j `grep processor /proc/cpuinfo | wc -l`
#编译很消耗系统资源，小内存可能编译通不过make install
make install
```

### 3.启动配置
```shell
ls -lrt /usr/local/mysql
# 创建启动脚本，并增加可执行权限
cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld
chmod +x /etc/init.d/mysqld
# 开机自启动
systemctl enable mysqld
```

### 4.修改Mysql配置文件
```shell

```

### 5.添加环境变量


### 6.初始化数据库

### 7.启动数据库