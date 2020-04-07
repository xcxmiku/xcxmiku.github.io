---
title: VPS的配置与优化
tags: Linux
categories: 技术分享
top: true
cover: false
img: https://cdn.jsdelivr.net/gh/xcxmiku/xcxmiku.github.io/medias/featureimages/22.jpg
abbrlink: a1f6425f
date: 2020-03-04 21:04:34
updated: 2020-03-06 20:43:14
---

因为某些原因重装了VPS，简单记录下重装后的配置。

### 连接

推荐一个颜值很高且多终端同步的SSH客户端[Termius](https://termius.com/)

![](https://i.loli.net/2020/03/05/Bv2ygLfKASeERlZ.png)

vps上重装的系统是Ubuntu 18.04 LTS Server - 64 Bit 

### 配置与优化

#### 更新源和软件

```
apt update

apt upgrade
```

#### 将语言设置为中文

```
dpkg-reconfigure locales
```

![](https://i.loli.net/2020/03/05/wu8cT5Ja1HqLVUS.png)

用空格选择全部zh开头的选项（也可只选择 zh_CN.UTF-8）

![](https://i.loli.net/2020/03/05/dJeg3DIp4CiMTB8.png)

设置系统默认语言为 zh_CN.UTF-8

![](https://i.loli.net/2020/03/05/ABkwtDFj37EScK1.png)

重新连接后默认语言就是中文了

#### 修改时区

```
timedatectl set-timezone Asia/Shanghai 
```

#### 修改vps默认的root密码

```
passwd root
```

![](https://i.loli.net/2020/03/05/IM3abWGiB5nwulD.png)

#### 安装常用工具

```
apt install vim wget git 
```

#### 安装BBR/锐速

```
wget -N --no-check-certificate "https://github.000060000.xyz/tcpx.sh" && chmod +x tcpx.sh && ./tcpx.sh
```

![](https://i.loli.net/2020/03/05/gdji4FrqzLhTY3M.png)

详见[Linux-NetSpeed](https://github.com/ylx2016/Linux-NetSpeed/releases)

#### 修改SSH端口

```
vim /etc/ssh/sshd_config
```

在原本的Port 22下面加一行（可能是注释的，请删除#号）

Port 2233 2233为需要修改的端口

![](https://i.loli.net/2020/03/06/sGKhCXPgHTwIkaL.png)

重启服务

```
systemctl restart sshd
```

查看是否监听了修改的端口

```
netstat -ntl
```

使用修改后的端口连接ssh

```
ssh -p 2233 root@127.0.0.1
```

如果无误，可将前面的Port 22注释掉

#### 配置免密登录

##### 生成密钥

```
ssh-keygen -t rsa
```

![](https://i.loli.net/2020/03/06/rKNmMd3SO1iITXb.png)

按提示输入即可（也可一直回车使用空密码）

公钥文件存储在C:\Users\15627\.ssh\id_rsa.pub

##### 将密钥上传至服务器

Linux下可直接使用ssh-copy-id

```
语法
ssh-copy-id [-i [identity_file]] [user@]machine
例子
ssh-copy-id -i ~/.ssh/id_rsa.pub root@127.0.0.1
```

![](https://i.loli.net/2020/03/06/TSQKWMfOhbw9Fn5.png)

Windows下目前没有自带ssh-copy-id，可使用scp

```
语法
scp [参数] [原路径] [目标路径]
例子
scp C:\Users\usernmae\.ssh\id_rsa.pub root@127.0.0.1:~/.ssh/win.pub
```

![](https://i.loli.net/2020/03/06/LtNS9aPdEY7B3vs.png)

然后使用ssh连接服务器将win.pub添加至authorized_keys

```
cd /root/.ssh && cat win.pub >> authorized_keys
```

即可免密登录vps

##### 配置第三方软件免密登录

[Xshell](https://www.netsarang.com/zh/xshell/)

菜单栏→工具→用户密钥管理者

![](https://i.loli.net/2020/03/06/yAWoQBiHdCfUpF7.png)

在用户密钥处导入id_rsa

![](https://i.loli.net/2020/03/06/IupVstZ5hENKW8R.png)

在用户身份验证中，方法选择Public Key，密钥选择刚导入的id_rsa即可

![](https://i.loli.net/2020/03/06/YWtmJD1qTL6zNid.png)

[Termius](https://termius.com/)

菜单栏→Keychain

![](https://i.loli.net/2020/03/06/CtAB6rNzFJdgMD5.png)

在Keychain里选择NEW KEY，在Add Key的Label\*处取个名，在Private Key\*点击File导入id_rsa并保存

![](https://i.loli.net/2020/03/06/kwb2YNRlATGWexU.png)

在连接设置里选择刚导入的key即可

![](https://i.loli.net/2020/03/06/qcxwvZa4dhIWpso.png)

#### 安装V2ray

具体参考

[V2Ray一键安装脚本](https://github.com/233boy/v2ray/wiki/V2Ray%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E8%84%9A%E6%9C%AC)