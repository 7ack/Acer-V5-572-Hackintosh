# Acer V5-572 On Sierra
- [Acer V5-572 On  Mavericks](http://bbs.pcbeta.com/viewthread-1501964-1-1.html)
- [Acer V5-572 On Yosemite ](http://bbs.pcbeta.com/viewthread-1618624-1-1.html)

## 配置

- CPU：i5-3337U 1.80GHz
- 显卡：HD4000 /nVidia GT750m
- 以太网卡：RTL8168
- 无线网卡：AR9285
- 声卡：ALC282


## 安装
1. 我安装的是论坛里的包含clover的原版镜像,论坛里有很多,你也可以用我下面提供的镜像
2. 安装过程不再复述,主要讨论我在安装过程中遇到的问题
3. 关于本人本次安装所有的相关附件链接: 

- [地址](https://pan.baidu.com/s/1c1ZtcFu) 提取码: cmjm

## 问题
### panic cpu问题
- 不知道是不是更新的缘故,发现clover首先加载的是`kexts/other`的驱动,而我吧之前系统的kexts放在了other文件夹中,导致fakesmc版本过低,于是从github上下载了最新版的fakesmc放在10.12文件夹下,清空other文件夹

### AptioFix加载超时以及memory allocation error
- 参考[新版 OsxAptioFix2Drv-64.efi详解](http://bbs.pcbeta.com/viewthread-1543092-1-1.html),删除`OsxAptioFixDrv`以及`lowMemFix`

### Mac os could not install
1. 检查自己的efi分区是否大于300m.因为我是在单硬盘单windows的基础上安装mac os,之前安装的时候efi分区只有150m.于是使用`DiskGenius`把windows安装时的恢复分区以及另一个分区删除,增加到efi分区.
2. 参考[http://bbs.pcbeta.com/viewthread-1704712-1-4.html](安装出现macOS could not be installed on your computer)的第二个方法,把`IAProductInfo`添加到u盘安装盘,可能用到windows读写hfs文件的工具也可以在链接中找到.并且这个方法可以直接跳过第一次安装!

### 内存识别为4G
- clover中添加内存信息.如果还不能识别的话,可以试试增加slot的数量.我就是加到4才能正确的识别.

### 声卡 网卡 蓝牙 触控板 电池电量显示
- 从RehabMan的[github](https://github.com/RehabMan)下载相应的最新补丁,安装

其他的问题可以在附件里找找
