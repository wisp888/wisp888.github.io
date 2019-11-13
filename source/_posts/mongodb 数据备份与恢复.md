---
title: mongodb 数据备份与恢复
date: 2019-11-05 17:16:26
tags: 
  - mongodb
categories: 
  - mongodb
---

### mongodump 备份数据库
```
./mongodump -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -o 文件存在路径
```
- 不指定-d 则导出所有数据库

<!--more-->

### mongorestore还原数据库
```
./mongorestore -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 --drop 文件存在路径
```
- --drop表示先删除所有记录，再导入备份

### mongoexport 备份数据库指定表
```
./mongoexport -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -c 表名 -f 字段 -q 条件导出 --type=csv -o 文件名
```
- -f 导出指定字段，以逗号分割
- -q 查询语句
- --type 默认为json

### mongoimport 导入表，或者表中部分字段
```
./mongoimport -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -c 表名 --type=csv --upsert --file 文件名
```
- --upsert 插入或者更新现有数据
- --type 指定备份文件的类型
- --headline 表示不导入首行


### 举个粟子
```
mongodump    -d oms -o ./oms.db
mongorestore -d oms --drop oms/
mongoexport  -d oms -c project_channel -o ./project_channel
mongoimport  -d oms -c project_channel project_channel
```