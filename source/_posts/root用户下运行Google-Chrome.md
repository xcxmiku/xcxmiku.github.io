---
title: Root用户下运行Google Chrome
tags: 技术分享
categories: 技术分享
abbrlink: '2e214463'
date: 2020-03-06 00:16:54
updated: 2020-03-06 00:16:54
---

#### 起因

[chromium的沙盒机制(sandbox)](https://chromium.googlesource.com/chromium/src/+/master/docs/linux/sandboxing.md)无法在root用户下运行

![](https://i.loli.net/2020/03/06/JcavdFy9s1DZx3I.png)

#### 解决方案

在命令行下输入如下代码即可启动

```
google-chrome --no-sandbox
```

如需通过桌面图标启动，请修改/usr/share/applications/google-chrome.desktop文件

```
Exec=/usr/bin/google-chrome-stable %U
```

改为

```
Exec=/usr/bin/google-chrome-stable --no-sandbox %U 
```

有三处，都添加--no-sandbox即可

![](https://i.loli.net/2020/03/06/HzcjWK4LXEqpumn.png)

![](https://i.loli.net/2020/03/06/5w182QBOUljTCvx.png)

![](https://i.loli.net/2020/03/06/5w182QBOUljTCvx.png)