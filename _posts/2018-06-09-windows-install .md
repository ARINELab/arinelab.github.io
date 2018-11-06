---
layout:     post
title:      "windows-install "
subtitle:   "windows无法完成安装"
date:       2018-06-09
author:     "Ray"
header-img: "img/win_install.jpg"
tags:
    - 技巧教程
    - 系统安装
---

### pe启动盘安装window ，系统释放的过程中提示下图所示

![811528509225_.pi](/img/win_install.jpg)

### First Step

`重启`

### Second Step

`重启仍然不行：`

1. shift + f10 唤出 命令提示符（shell）
2. 输入`regedit`,唤出注册表编辑器 HKEY_LOCAL_MACHINE-->SYSTEM-->Setup-->Status-->ChildCompletion-->setup.exe
3. 打开 setup.exe ，将 1 改成 3


![801528509224_.pi](/img/15285088496606/801528509224_.pic.jpg)

### Third Step 

`仍然不行`

(目前pe盘中系统非ghost版本）重新进入pe启动盘，
选择【12】安装原版win7/wwin8系统 ,此举对win10 无效。 
![831528511077_.pi](/img/15285088496606/831528511077_.pic.jpg)

打开解压iso系统包，点击setup.exe,等待重新安装

跳出window无法安装。重复上述步骤。

### Forth Step

下载`ghost`版本（系统之家）,进入pe盘，双击打开.iso镜像文件，set.up.exe --> 根据引导执行，稍安勿躁。

### Fifth Step

`仍然不行`

更换系统镜像
    
```
https://msdn.itellyou.cn/  ---> 下载纯净版镜像，需要迅雷解码下载链接（还有各类windows 的软件安装包）
http://www.yishimei.cn/  ---> 各类教程、工具的下载
http://www.yishimei.cn/network/319.html  --> windows 激活工具下载
http://www.bigbaicai.com/   ---> 启动盘目前是用他们家制作，教程网站都有。
            `老毛桃`、`u深度``，`u大侠`等等，自行更换。如出一辙。
```


### Sixth Step

`仍然不行`

换个人



