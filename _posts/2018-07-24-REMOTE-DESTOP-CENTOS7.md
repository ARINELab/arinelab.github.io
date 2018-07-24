---

layout:     post
title:      "CENTOS7 基于XVNC配置远程桌面可访问与用户权限"
subtitle:   "基于vnc设置centos7可远程桌面访问"
date:       2018-07-24
author:     "ray"
header-img: "img/aria2.jpg"
tags:

```
- 远程桌面
- 技巧教程
```

---

## CENTOS7 XVNC 远程桌面与用户权限

### 1. 首先是桌面GUI环境

```
# GNOME为例
yum groupinstall "GNOME Desktop"
# reboot
```

### 2. VNC 服务器

```
# tigerVNC,一个开源的VNC服务应用
yum install tigervnc-server
```

### 3. 新添加一个可以远程访问的用户

```
useradd arine
passwd arine
```

当用户尝试使用VNC访问远程桌面时，VNC守护进程就会启动来处理这个请求，需要为每个用户创建一个独立的VNC配置文件。

CentOS依靠systemd来管理和配置系统服务，将使用systemd来为用户xmodulo配置VNC服务器。

### 4. 检查服务器VNC服务器状态

```
# 如图表示未激活
systemctl status vncserver@:.service
systemctl is-enabled vncserver@.service
```

![5b5698143d157](https://i.loli.net/2018/07/24/5b5698143d157.jpg)

### 5. 为用户单独创建一个VNC服务配置

```
# '1' 是 配置命名,第二个用户可以是2,以此类推
cp /lib/systemd/system/vncserver@.service/etc/systemd/system/vncserver@:1.service
```

```
vi /etc/systemd/system/vncserver@:1.service
# 或者
# gedit /etc/systemd/system/vncserver@:1.service
```

替换文件中`<Users>` 为新建的用户 `arine`

```
# 编辑 vncservers
vi /etc/sysconfig/vncservers
```

```
VNCSERVERS="1:arine"
VNCSERVERARGS[1]="-geometry 1366x768" # 屏幕分辨率 1024×768
```

```
# 设置登录权限
vim /etc/group
```

```
# 末尾添加:
arine:x:250:arine
```

### 6. 为远程连接设置密码(可选)

```
# 注意, `-` 两边都有空格
su - arine
vncpasswd

# 重新加载,使得服务生效
systemctl daemon-reload
systemctl enable vncserver@:1.service  # 启动时自动启动VNC服务
netstat -tulpn | grep vnc # 检查VNC正在监听的端口
```

### 7. 设置防火墙

```
vi /etc/sysconfig/iptables
```

```
# 使得防火墙开放80端口
-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT  

# for vncserver
-A INPUT -p tcp -m tcp --dport 5901:5903 -j ACCEPT 

# for xrdp
-A INPUT -m state --state NEW -m tcp -p tcp--dport 3389 -j ACCEPT
```

### 8. 防火墙状态

```
service iptables status # 查看
service iptables stop #暂停
chkconfig off
service iptables restart # 重启
```

### 9. 将远程访问例如开机即加载启动

```
chkconfig xrdp on
chkconfig vncserver on
```

### 10. 远程桌面

windows会自动选择 Xvnc协议.

linux: 远程桌面连接,选择 vnc协议

登录是密码则是登录账号与密码

### 11. VNC viewer

`因为出现部分windows连接正常,部分不正常,mac无法使用paraellels client 登录.我们使用了VNC Viewer `

第5步中新建的1.service ,所以我们如下图配置

![5b570c27a7bb7](https://i.loli.net/2018/07/24/5b570c27a7bb7.png)

![5b570cdbac990](https://i.loli.net/2018/07/24/5b570cdbac990.png)

`注意: 此处的密码则是第6步设置的 vncpasswd `

### 13. 参考资料

1. http://tigervnc.org/doc/Xvnc.html
2. https://blog.csdn.net/kwame211/article/details/76014361
3. http://os.51cto.com/art/201103/249398.htm
