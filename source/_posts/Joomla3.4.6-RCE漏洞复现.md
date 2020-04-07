---
title: Joomla3.4.6-RCE漏洞复现
tags: 漏洞复现
categories: 技术分享
abbrlink: 604d2f58
date: 2019-10-23 18:13:36
updated: 2019-10-23 18:13:36
---

### 环境搭建

源码下载地址

```
https://github.com/joomla/joomla-cms/releases/tag/3.4.6
https://downloads.joomla.org/it/cms/joomla3/3-4-6 
```

 我这边直接使用了phpstudy

![](https://i.loli.net/2019/10/23/Kmkp3qgMayu6rZf.png) 

![](https://i.loli.net/2019/10/23/gcO72WNJht5eRBT.png)

这里需要提前新建数据库，然后直接安装即可。

![](https://i.loli.net/2019/10/23/C9WxVTsIi7Pjop3.png)

 安装完成后的默认后台地址为

```
http://127.0.0.1/joomla/administrator/
```

###  **漏洞利用** 

POC在这里

```
 https://github.com/kiks7/rusty_joomla_rce
```

###### 使用python进行漏洞检测

```
python rusty_joomla_exploit.py -t http://10.1.1.11/joomla/ -c   
```

![](https://i.loli.net/2019/10/23/tOe7n5WLQy8UP6i.png)

###### 漏洞利用

![](https://i.loli.net/2019/10/23/HXMYL6n7WOueD5A.png)

反弹shell失败，但是在 configuration.php 写入了一句话木马，使用蚁剑连接

![](https://i.loli.net/2019/10/23/Ns2j8I1Rbh4SAdT.png)

可getshell



###### 使用msf进行漏洞检测

将metasploit_rusty_joomla_rce放到/usr/share/metasploit-framework/modules/exploits/multi/php/

```
cp metasploit_rusty_joomla_rce.rb /usr/share/metasploit-f
ramework/modules/exploits/multi/php/
```

然后在 msfconsole 里 reload_all 再搜索 joomla 

![](https://i.loli.net/2019/10/23/5OosHyXm6iJVrkw.png)

选择模块并查看参数

![](https://i.loli.net/2019/10/23/Kd4JqNcDm9fY1jM.png)

设置参数进行攻击

![](https://i.loli.net/2019/10/23/KwhXSxFQZukpLGO.png)

还需设set verbose true

![](https://i.loli.net/2019/10/23/m8uAOQiI9sYyLWN.png)

能获取到漏洞所需要的信息，但却还是利用失败，原因未知。