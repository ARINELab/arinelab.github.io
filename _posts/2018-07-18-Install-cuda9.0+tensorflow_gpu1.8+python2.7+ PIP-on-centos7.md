---
layout:     post
title:      "Install cuda9.0+tensorflow_gpu1.8+python2.7+ PIP-on-centos7"
subtitle:   "install tensorflow gpu"
date:       2018-07-18
author:     "ray"
header-img: "img/TensorFlow.jpg"
tags:
    - tensourflow_gpu
    - 技巧教程
    - Centos7
---

## 在centos7 安装cuda9.0+tensorflow_gpu1.8+python2.7+pip

##基本信息

```
[ray@localhost ~]$ cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"

```

```
[ray@localhost ~]$ cat /etc/centos-release
CentOS Linux release 7.5.1804 (Core) 
```

```
[ray@localhost ~]$ lspci | grep -i nvidia
03:00.0 VGA compatible controller: NVIDIA Corporation GM206GL [Quadro M2000] (rev a1)
03:00.1 Audio device: NVIDIA Corporation Device 0fba (rev a1)

```
如果没有识别出GPU卡。`update-pciids`更新下

```
#检查是否有显卡驱动
 nvidia-smi
```

`显示没有命令则没有安装`

如果没有，则 	`yum install gcc gcc-c++`安装

```
[ray@localhost ~]$ gcc -v
使用内建 specs。
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/usr/libexec/gcc/x86_64-redhat-linux/4.8.5/lto-wrapper
目标：x86_64-redhat-linux
配置为：../configure --prefix=/usr --mandir=/usr/share/man --infodir=/usr/share/info --with-bugurl=http://bugzilla.redhat.com/bugzilla --enable-bootstrap --enable-shared --enable-threads=posix --enable-checking=release --with-system-zlib --enable-__cxa_atexit --disable-libunwind-exceptions --enable-gnu-unique-object --enable-linker-build-id --with-linker-hash-style=gnu --enable-languages=c,c++,objc,obj-c++,java,fortran,ada,go,lto --enable-plugin --enable-initfini-array --disable-libgcj --with-isl=/builddir/build/BUILD/gcc-4.8.5-20150702/obj-x86_64-redhat-linux/isl-install --with-cloog=/builddir/build/BUILD/gcc-4.8.5-20150702/obj-x86_64-redhat-linux/cloog-install --enable-gnu-indirect-function --with-tune=generic --with-arch_32=x86-64 --build=x86_64-redhat-linux
线程模型：posix
gcc 版本 4.8.5 20150623 (Red Hat 4.8.5-28) (GCC) 

```


## 安装kernel header和相关开发包
`yum install kernel-devel-$(uname -r) kernel-headers-$(uname -r)`


```
#配置第三方RPM源
[root@gemfield.org ~]# rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org

[root@gemfield.org ~]# rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
获取http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
准备中...                          ################################# [100%]
正在升级/安装...
   1:elrepo-release-7.0-3.el7.elrepo  ################################# [100%]
```


## 禁用默认nouveau驱动
`原因： 与nvidia驱动冲突`


1.`/lib/modprobe.d/dist-blacklist.conf`  将nvidiafb注释掉
2.再在该文件中添加一下配置：

```
blacklist nouveau  
options nouveau modeset=0 
```

## 重建initramfs image
如果/boot 分区大小不够，可以备份到其他目录

```
mv /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r).img.bak  
dracut /boot/initramfs-$(uname -r).img $(uname -r) 
```

## 重启系统
## 确认nouveau是否被禁用

```
没有输出则 ok！
[root@gemfield.org ~]# lsmod | grep nouveau
[root@gemfield.org ~]#
```

## 下载cuda_x.x.xxx_xxx.xx_linux.run

```
<https://developer.nvidia.com/cuda-90-download-archive?target_os=Linux&target_arch=x86_64&target_distro=CentOS&target_version=7&target_type=runfilelocal>

#9.2 的cuda 提供的是9.2的lincublas，tensorflow_gpu 1.8 需要libcublas 9.0。
#所以cuda装9.0的就行了
```

```
sudo sh cuda_9.0.176_384.81_linux.run #提示什么都是 accept ，yes ，或者直接回车

```

可能会提示驱动其他什么不对。 然后 希望带上 --silent --driver --samples 等等参数，提示什么带上什么。

## 再一次重启
```
[ray@localhost ~]$ nvidia-smi 
Mon Jul  9 19:18:46 2018       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 396.26                 Driver Version: 396.26                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Quadro M2000        On   | 00000000:03:00.0  On |                  N/A |
| 56%   35C    P8     9W /  75W |    262MiB /  4043MiB |      3%      Default |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|    0      2073      G   /usr/bin/X                                    70MiB |
|    0      3271      G   /usr/bin/gnome-shell                         108MiB |
|    0      4528      G   ...-token=21D0A49A25D99CA82557E0D077F8D389    80MiB |
+-----------------------------------------------------------------------------+

```

如上所是，则是nvidia驱动也顺带装好了。

### 注意
也可以先下载nvidia驱动<https://www.nvidia.cn/Download/index.aspx?lang=cn>，
```
sh NVIDIA-Linux-x86_64-xxx.run #根据提示安装，但是记得选择好版本
```

## 配置环境变量

`gedit ~/.bashrc`

### 写入bashrc文件保存 
```
gpu driver export CUDA_HOME=/usr/local/cuda-9.0 export PATH=/usr/local/cuda-9.0/bin:$PATH export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:$LD_LIBRARY_PATH export LD_LIBRARY_PATH="/usr/local/cuda-9.0/lib:${LD_LIBRARY_PATH}" 
```

### 然后source下
```
source ~/.bashrc  
nvidia-smi 
``` 

### 验证结果

```
[ray@localhost ~]$ nvcc -V 
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2017 NVIDIA Corporation
Built on Fri_Sep__1_21:08:03_CDT_2017
Cuda compilation tools, release 9.0, V9.0.176

```

## 安装DKMS依赖
```
yum install -y elfutils-libelf-devel
rpm -ivh dkms-2.4.0-1.20170926git959bd74.el7.noarch.rpm
```

## 安装元数据包源
```
<https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/cuda-repo-rhel7-9.0.176-1.x86_64.rpm>

sudo rpm -ivh cuda-repo-rhel7-9.0.176-1.x86_64.rpm

#更新元数据
sudo yum clean expire-cache
sudo yum update
```

## 安装cudnn
`<https://developer.nvidia.com/rdp/cudnn-download> 下载forCUDA9.0`


```
$ tar -xvzf cudnn-9.0-linux-x64-v6.0.tgz $ cp -P cuda/include/cudnn.h /usr/local/cuda-9.0/include 
$ cp -P cuda/lib64/libcudnn* /usr/local/cuda-9.0/lib64 
$ chmod a+r /usr/local/cuda-9.0/include/cudnn.h /usr/local/cuda-9.0/lib64/libcudnn*

```

## Test

```
cd /usr/local/cuda-9.0/sample
make -C 0_Simple/vectorAdd
make -C 0_Simple/vectorAddDrv/
make -C 1_Utilities/bandwidthTest/
make -C 1_Utilities/deviceQuery
```
## 然后直接执行测试，pass则驱动及cuda安装无问题

```
[ray@localhost samples]$ ./bin/x86_64/linux/release/deviceQuery
./bin/x86_64/linux/release/deviceQuery Starting...

 CUDA Device Query (Runtime API) version (CUDART static linking)

Detected 1 CUDA Capable device(s)

Device 0: "Quadro M2000"
  CUDA Driver Version / Runtime Version          9.2 / 9.0
  CUDA Capability Major/Minor version number:    5.2
  Total amount of global memory:                 4043 MBytes (4239785984 bytes)
  ( 6) Multiprocessors, (128) CUDA Cores/MP:     768 CUDA Cores
  GPU Max Clock rate:                            1162 MHz (1.16 GHz)
  Memory Clock rate:                             3303 Mhz
  Memory Bus Width:                              128-bit
  L2 Cache Size:                                 786432 bytes
  Maximum Texture Dimension Size (x,y,z)         1D=(65536), 2D=(65536, 65536), 3D=(4096, 4096, 4096)
  Maximum Layered 1D Texture Size, (num) layers  1D=(16384), 2048 layers
  Maximum Layered 2D Texture Size, (num) layers  2D=(16384, 16384), 2048 layers
  Total amount of constant memory:               65536 bytes
  Total amount of shared memory per block:       49152 bytes
  Total number of registers available per block: 65536
  Warp size:                                     32
  Maximum number of threads per multiprocessor:  2048
  Maximum number of threads per block:           1024
  Max dimension size of a thread block (x,y,z): (1024, 1024, 64)
  Max dimension size of a grid size    (x,y,z): (2147483647, 65535, 65535)
  Maximum memory pitch:                          2147483647 bytes
  Texture alignment:                             512 bytes
  Concurrent copy and kernel execution:          Yes with 1 copy engine(s)
  Run time limit on kernels:                     Yes
  Integrated GPU sharing Host Memory:            No
  Support host page-locked memory mapping:       Yes
  Alignment requirement for Surfaces:            Yes
  Device has ECC support:                        Disabled
  Device supports Unified Addressing (UVA):      Yes
  Supports Cooperative Kernel Launch:            No
  Supports MultiDevice Co-op Kernel Launch:      No
  Device PCI Domain ID / Bus ID / location ID:   0 / 3 / 0
  Compute Mode:
     < Default (multiple host threads can use ::cudaSetDevice() with device simultaneously) >

deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 9.2, CUDA Runtime Version = 9.0, NumDevs = 1
Result = PASS

```
###常见错误一

```
如果不通过，那就是nvidia driver 和cuda 的driver 版本不匹配。笔者是多次冲突后，
直接让cuda覆盖原有的nvidia驱动（需要nouveau已经禁止才可以生效）。
```

## 安装tensorflow_gpu
```
pip install --upgrade tensorflow-gpu
## 如果 pip install --upgrade tensorflow-gpu == 1..2 则安装1.2版本。
## 安装其他版本前，先 pip uninstall xxxxxxx
```

### 常见错误二

```
“ImportError: libcublas.so.8.0: cannot open shared object file: No such file or directory”

那就将cuda tookit的版本降为8.0 。笔者在这里装9.2 一直报9.0，换9.0的cuda就可以了
```


## 参考资料

1. https://www.pugetsystems.com/labs/hpc/Install-TensorFlow-with-GPU-Support-the-Easy-Way-on-Ubuntu-18-04-without-installing-CUDA-1170/
2. https://docs.nvidia.com/cuda/index.html
3. https://www.jianshu.com/p/ca0cb47ad9a8
4. https://www.mtyun.com/library/how-to-install-tensorflow-on-centos7
5. https://github.com/tensorflow/tensorflow/issues/15604



