---
title: Burp Suite v2020.2.1的汉化以及无cmd框启动
keywords: [Burp Suite,burpsuit,burpsuit汉化,Burp Suite汉化,无框启动,汉化,Burp Suite v2020.2.1]
tags: 资源分享
categories: 资源分享
top: false
cover: false
img: https://cdn.jsdelivr.net/gh/xcxmiku/xcxmiku.github.io/medias/featureimages/23.jpg
abbrlink: 38a7a949
date: 2019-12-02 22:20:02
updated: 2020-03-26 23:41:35
---

## 下载

最近几个burp suite的破解版都来源于四叔

http://scz.617.cn:8/misc/201910151519.txt

https://www.52pojie.cn/thread-1038295-1-1.html

以下是我打包的版本（包括图标，汉化包，启动的bat和vbs， [knife插件](https://github.com/bit4woo/knife)）

[Burp_Suite_Pro_v2020.2.1.zip](https://pan.baidu.com/s/1Q8M8Fq_uIvMhr6s6Fyfycg)  提取码: ssnb

| 算法   | 哈希值                                                       |
| :----- | ------------------------------------------------------------ |
| SHA256 | 2E511B3602F4859CCB5E5604E3697AB0BCA84561B0E3FF0755AC8A0204D88019 |
| MD5    | 699210589DC72FAA5C0A6EABC9D0BEA5                             |

或者下载以下的原版jar

[burpsuite_pro_v2020.2.1.jar](https://mega.nz/#!hB5XwRrK!SavleJzp0FKl71TCkvtqpEhZGQ9wYLlRxnAKYoHVlZs)

| 算法   | 哈希值                                                       |
| ------ | ------------------------------------------------------------ |
| SHA256 | 7e95e1cec919419db62b73e50d74e6feaf70755ec828562cb22343fef433c1df |
| MD5    | f6b13a530b592fdc083373db6e89e41a                             |

[官网](https://portswigger.net/burp/releases)

![](https://i.loli.net/2020/03/26/rRQDwybjOdAnMSP.png)

[burp-loader-keygen-2020_2_1.jar](http://scz.617.cn/private/burp-loader-keygen-2020_2_1.jar_568321911fea077bc290bd9914b73402)

| 算法   | 哈希值                                                       |
| ------ | ------------------------------------------------------------ |
| SHA256 | 74893842a782238f52f0f225c06fa744568321911fea077bc290bd9914b73402 |
| MD5    | e646b3eef03efba637e6ed794ced4114                             |



## 汉化

原版启动的bat为

```
java -noverify -Xbootclasspath/p:burp-loader-keygen-2020_2_1.jar -jar burpsuite_pro_v2020.2.1.jar
```

加载汉化包即可汉化

```
java -noverify -Dfile.encoding=utf-8 -javaagent:BurpSuiteChs.jar -Xbootclasspath/p:burp-loader-keygen-2020_2_1.jar -Xmx1024m -jar burpsuite_pro_v2020.2.1.jar
```

## 无cmd框启动

使用vbs脚本调用bat即可

```
Set shell = Wscript.createobject("wscript.shell")
a = shell.run ("startCN.bat",0)
```

## 创建桌面图标

桌面右键，新建快捷方式，指定vbs脚本，然后更改图标即可

## 效果图

桌面图标

![](https://i.loli.net/2019/12/02/dpcsBjo9NyPVnLO.png)

最终效果

![](https://i.loli.net/2020/03/26/3bzJufiOkQPMHdT.png)



