---
title: Hexo+Git+Nginx的配置
tags: Linux
categories: 技术分享
abbrlink: 63671
date: 2020-03-13 21:46:23
updated: 2020-03-14 02:46:23
---

### 安装Nginx

Ubuntu官方源里有nginx的稳定版，我这里选择最新版进行安装

[官方指南](http://nginx.org/en/linux_packages.html#Ubuntu)

##### 添加源

```
echo "deb [arch=amd64 ] http://nginx.org/packages/mainline/ubuntu `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list
```

##### 导入签名密钥

```
curl -fsSL https://nginx.org/keys/nginx_signing.key | sudo apt-key add -
```

##### 安装

```
sudo apt update
sudo apt install nginx
```

![](https://i.loli.net/2020/03/13/COdYMJzj3PZDm7w.png)



### 安装Git

Ubuntu官方源里有git的稳定版，我这里选择最新版进行安装

[官方指南](https://git-scm.com/download/linux)

##### 使用add-apt-repository命令添加源（会自动导入密钥）

```
sudo add-apt-repository ppa:git-core/ppa
```

##### 安装

```
sudo apt update
sudo apt install git
```

![](https://i.loli.net/2020/03/13/F95UfXHuSsZzy8G.png)

### 配置Git

在vps上配置git，方便hexo deploy

[官方指南](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E9%85%8D%E7%BD%AE%E6%9C%8D%E5%8A%A1%E5%99%A8)

##### 新建用户

```
sudo adduser git
```

![](https://i.loli.net/2020/03/13/ZgDLfyja569oYlX.png)

##### 配置authorized_keys

```
su git
cd
mkdir .ssh && chmod 700 .ssh
touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
```

##### 导入用户id_rsa

详见[VPS的配置与优化](https://xcxmiku.com/archives/a1f6425f/#toc-heading-10)

##### 限制 Git 用户使用SSH登陆

将git-shell添加到/etc/shells（git-shell的路径一般为/usr/bin/git-shell）

![](https://i.loli.net/2020/03/13/E53hAwoMkQ69JxX.png)

把git默认的shell替换成git-shell

```
sudo chsh git -s $(which git-shell)
```

或者直接修改/etc/passwd文件，将

git:\x:1000:1000:,,,:/home/git:/bin/bash

改为

git:\x:1000:1000:,,,:/home/git:/usr/bin/git-shell

### 创建Git仓库

##### 初始化仓库

```
cd /home/git/
git init --bare blog.git
```

##### 更改权限

```
sudo chown -R git:git blog.git
```

##### 测试git clone

```
git clone git@vpsip:~/blog.git 
```

如果修改ssh端口请使用以下命令（xxx为端口号）

```
git clone ssh://git@vpsip:xxx/~/blog.git
```

##### 建立tmp和blog目录

```
mkdir -p /home/git/tmp
mkdir -p /home/git/blog
```

##### 设置git post-receive 钩子

编辑/home/git/blog.git/hooks/post-receive文件

```
#!/bin/bash -l
GIT_REPO=/home/git/blog.git
TMP_GIT_CLONE=/home/git/tmp
PUBLIC_WWW=/home/git/blog
rm -rf ${TMP_GIT_CLONE}
git clone $GIT_REPO $TMP_GIT_CLONE
rm -rf ${PUBLIC_WWW}
cp -rf ${TMP_GIT_CLONE} ${PUBLIC_WWW}
```

##### 修改各个文件夹权限

```
chmod +x /home/git/blog.git/hooks/post-receive
chmod 777 -R /home/git/blog.git
chmod 777 -R /home/git/blog
chmod a+r /home/git/blog
chmod 777 -R /home/git/tmp
chown -R git:git /home/git/
```

### Hexo配置

修改_config.yml文件里的deploy字段

```
deploy:
- type: git
  repository: git@vpsip:~/blog.git
  branch: master
```

如果修改ssh端口请将repository修改为（xxx为端口号）

```
ssh://git@vpsip:xxx/~/blog.git
```

### Nginx配置

推荐直接使用digitalocean的nginx在线配置生成

[链接](https://www.digitalocean.com/community/tools/nginx)   [源码](https://github.com/digitalocean/nginxconfig.io)

![](https://i.loli.net/2020/03/14/tTfH2Dmph5uCaZM.png)

按顺序填写完需求后，下载配置文件至vps，解压即可，也可自行复制粘贴



Nginx全局配置文件为/etc/nginx/nginx.conf，为了防止错误修改全局文件，我选择修改/etc/nginx/conf.d文件夹里的子项目配置文件default.conf

配置SSL和网站路径

```
server{
    listen 443; #监听443端口
    server_name xxxx.com; #配置域名
    ssl on; #开启SSL
    ssl_certificate domain.cert; #证书
    ssl_certificate_key private.key; #私钥
    location / {
        root  /home/git/blog; #网站路径
        index  index.html index.htm;
    }  
    }
```

配置HTTP跳转至HTTPS

```
server {
    listen 80; #监听443端口
    server_name xxxx.com; #配置域名
    return 301 https://$server_name$request_uri; #跳转
}
```

防止列目录

```bash
http {
autoindex off;
}
```

不知响应头里显示nginx版本号

```bash
http {
server_tokens off;
}
```

配置完后使用

```
nginx -t
```

检测是否有误

![](https://i.loli.net/2020/03/14/CqTMSrveymkOa4Q.png)

启动nginx

```
systemctl enable nginx
systemctl start nginx
nginx -s reload
```

