---
title: XML外部实体注入(XXE)
tags: XXE
categories: 技术分享
abbrlink: '81510889'
date: 2019-10-16 21:41:29
updated: 2019-10-16 21:41:29
---

## 前言

XXE Injection即XML External Entity Injection,也就是XML外部实体注入攻击.漏洞是在对非安全的外部实体数据进行处理时引发的安全问题.

在XML1.0标准里,XML文档结构里定义了实体(entity)这个概念.实体可以通过预定义在文档中调用,实体的标识符可访问本地或远程内容.如果构造特殊的实体就可以做到任意文件读取。

想了解XXE可以去看先知社区的这篇文章[一篇文章带你深入理解漏洞之 XXE 漏洞](https://xz.aliyun.com/t/3357)。



## 漏洞发现

抓包某登录框，发现是使用XML传输切会回显登录用户名

![](https://i.loli.net/2019/10/28/XuiFc5BDROknwpe.png)

## 漏洞利用

构建外部实体读取etc/passwd成功

![](https://i.loli.net/2019/10/28/6bYKjNZfDOJ4L5P.png)