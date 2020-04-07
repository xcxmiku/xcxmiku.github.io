---
title: 凡诺CMS文件包含漏洞
tags: 文件包含
categories: 技术分享
abbrlink: b0c3a4a4
date: 2019-10-16 18:04:23
updated: 2019-10-16 18:04:23
---

![文件包含8.png](https://i.loli.net/2019/10/28/BZEiyAfn6vCF8OD.png)

在后台添加频道处可以上传图片和附件，可以上传图片或上传以下格式的附件

![](https://i.loli.net/2019/10/28/N2Vy1WZzpvDERY7.png)

图片马的制作在上篇[文章](https://xcxmiku.github.io/2019/10/15/LFI%E6%9C%AC%E5%9C%B0%E6%96%87%E4%BB%B6%E5%8C%85%E5%90%AB/)说过，这里我将冰蝎马改名为shell.doc进行上传，上传后会回显文件地址。

![](https://i.loli.net/2019/10/28/jDT5OdbHMoIPure.png)

然后将回显的文件地址填在频道模板处，在地址前面加上../../（用于跳出文件目录）

![](https://i.loli.net/2019/10/28/BlRUQC8MEidTwaJ.png)

设置频道名称后添加频道，在首页找到添加的频道打开

![](https://i.loli.net/2019/10/28/R4uBDmyFlXGCVns.png)

页面并没有显示任何东西，因为被当成PHP解析了。

使用冰蝎连接即可getshell！

![](https://i.loli.net/2019/10/28/fqK3x4MarVvlo6Z.png)
