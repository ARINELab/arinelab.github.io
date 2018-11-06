---

layout:     post
title:      "U盘安装centos7, legacy"
subtitle:   "U盘安装centos7, legacy"
date:       2018-07-18
author:     "ray"
header-img: "img/centosInstall.jpg"
tags:
    - 技巧教程
    - 系统安装
---

## u盘安装centos7

### 准备工具

```
1. 容量允许的优盘
2. 下载 UltraISO;
3. CentOS7光盘镜像;
```

```
http://mirrors.aliyun.com              阿里云镜像站
https://mirrors.tuna.tsinghua.edu.cn   清华大学镜像站
http://mirrors.sohu.com/               搜狐镜像站
http://mirrors.163.com/                网易镜像站
```

### UltraISO将ISO镜像刻录到U盘

```
1. 安装UltraISO, 并选择试用
2. 进入主界面
3. 菜单栏 --> 启动 --> 写入硬盘映像
4. 选择要写入的优盘或者硬盘. (会被格式化的,建议将其他外界存储设备暂时拔除)
5. 写入
6. 便捷启动---> syslinux(如果无法引导系统,则需要执行本步骤)
7. (必做) 修改u盘名为 'C7' (记住you盘名,后面要用)
```

## 重启需要安装的计算机

### 一旦看到电脑品牌启动logo,说明固件启动,立刻按f2

`部分台式机是delete,部分笔记本是f1`
设置可以 legacy 引导启动.

### 重启,固件启动按 f12

`部分台式机是 esc`
选择优盘启动 (从优盘品牌, u盘名等信息判断, 还有什么 xxxStorage 也可能,小概率 .)

### 进入到linux引导界面

![5b57cd2bee3e7](https://i.loli.net/2018/07/25/5b57cd2bee3e7.png)

 按下tab 按键, 
 
将语句改成:
`initrd=initrd.img inst.stage2=hd:LABEL=C7 quiet`

`C7` 是U盘名字



#### 注意: UEFI引导看这里

`UEFI 不是按Tab 而是按下 e , 修改完成后 (ctrl+x 确认)`

### 根据引导

根据引导一步步执行, 需要桌面环境的不要选择最小安装. 可以选择GNOME 或者KDE 环境

## 参考

1. https://www.linuxidc.com/Linux/2017-11/148355.htm
2. https://blog.csdn.net/gsls200808/article/details/49717813
3. https://blog.csdn.net/Jeffaryhe/article/details/75042971


