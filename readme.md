# Acer V5-572 On Mojave

## 配置

- CPU：Intel(R) Core(TM) 7 Series i5-3337U @ 1.80GHz Ivy Bridge HM77 
- Cores : 2 Cores, 4 Threads @ 1796MHz Bus: 24MHz FSB: 96MHz
- RAM : 2x4096Mb HibernateMode: 0
- 显卡：HD4000 ID:0x01660009 /Nvidia GT750m
- 以太网卡：RTL8168
- 无线网卡：BCM94352
- 声卡：ALC282
- Clover version: v4895
- ProductName  : Mac OS X 10.14.3 BuildVersion: 18D109
- Bootargs: kext-dev-mode=1 dart=0 nv_disable=1 -alcbeta 

## 现状
- [x] 核显开启QE/CI Metal  独显bios屏蔽睡眠 
- [x] 声卡 
- [x] 有线网卡
- [x] Wi-Fi + 蓝牙
- [x] USB识别.供电.睡眠.速率
- [x] 电池电量显示
- [x] 原生电源管理.CPU变频
- [x] 触控板(不支持三指以上手势) .键盘/灯.亮度调节
- [x] EDID注入,开启HIDPI
- [ ] iCloud.iMessage.Facetime.Handoff.Universal-Clipboard.Airdrop.Continuity.Apple-watch解锁
- [ ] HDMI (未测试)
- [ ] 笔记本风扇转速显示
- [ ] 开机第二阶段闪屏
- [ ] 偶尔蓝牙固件升级失败导致蓝牙WI-FI失效





## 安装
安装前仔细阅读[Mojave Laptop Support](https://www.tonymacx86.com/forums/mojave-laptop-support.196/)

*我是10.12.3保资料升级安装10.14.3*

### 安装中的问题

> 安装中出现的IOConsoleUsers: time(0) 0->0, lin 0, llk 1, IOConsoleUsers: gIOScreenLockState 3, hs 0, bs 0, nov 0, sm 0x0
   - 原因是系统无法识别出你的显卡驱动，解决方法：
    
    勾选Inject Intel,将platform-id修改为0x12345678即可进入安装界面

> 安装后无法进入系统,显示kext stall AppleApcicpu
   - 原因是fakeSmc.kext不兼容,解决方法:
   1. 重启进入RecoveryHD,打开终端,进入系统分区
        cd /volumes/mac/system/library/Extensions或者/library/Extensions
   2. 删除之前安装的不兼容驱动

        rm -rf xxx.kext

        (为了避免其他驱动的不兼容,我删除了之前安装的所有驱动)
   1. 重建缓存

        kextcache -i /volumes/mac


### 安装完成后问题:
由于Mojave本身原因以及删除了所有驱动,遇到的问题比较多:
1. 睡眠失效
2. 蓝牙失效
3. 声卡失效
4. 亮度调节失效
5. 屏蔽独显失效
6. USB不供电.限速


### 解决问题
[参考教程1](https://www.tonymacx86.com/threads/an-idiots-guide-to-lilu-and-its-plug-ins.260063/)
[参考教程2](https://www.tonymacx86.com/threads/faq-read-first-laptop-frequent-questions.164990/)


#### WiFi蓝牙
> wifi
  
  使用[AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup)	

> 蓝牙
1.  [BrcmPatchRAM2](https://github.com/RehabMan/OS-X-BrcmPatchRAM):can be installed either through Clover kext injection or placed in /Library/Extensions on 10.11 and later.
2. [BrcmFirmwareRepo.kext](https://github.com/RehabMan/OS-X-BrcmPatchRAM): Install to /Library/Extensions (on 10.11 and later). This kext is much more memory efficient than  BrcmFirmwareData.kext and is the preferred configuration.BrcmFirmwareRepo.kext does not work with Clover kext injection, unless using a device specific firmware injector. 



> Apple watch~~无法~~解锁

  *检测到Apple Watch，并尝试用Apple Watch解锁，但是菊花转了半天后出现了密码的输入框*
- 注销iCloud后重新登录iCloud并打开使用Apple watch解锁



####  声卡
> [AppleALC](https://github.com/acidanthera/AppleALC)
1. 注入id3,27,127均失败
2. 添加参数开启debug模式,重启
3. 终端输入:log show --predicate 'process == "kernel" AND (eventMessage CONTAINS "AppleALC" )' --style syslog --source
4. 输出:(kernel) AppleALC: init @ automatically disabling on an unsupported operating system
5. 添加-alcdbg -liludbg
6. 输出:(kernel) AppleALC:     alc @ failed to obtain device info for analog controller (1)
7. 怀疑没有加载AppleHDA
8. 输入:kextstat | grep 'Lilu\|AppleALC\|AppleHDA'
9. clover/acpi添加 fix HPET 
10. 重启加载成功

> HDMI音频输出
- [TBD:个人不是很需要HDMI ](https://www.tonymacx86.com/threads/guide-intel-igpu-hdmi-dp-audio-all-sandy-bridge-kaby-lake-and-likely-later.189495/)

#### 亮度调节
> [AppleBacklightFixup](https://bitbucket.org/RehabMan/applebacklightfixup/downloads/)

1.SSDT-PNLF.aml in ACPI/patched

~~2. AppleBacklightFixup.kext installed to /L/E~~


2.[WhateverFreen.kext调节笔记本亮度](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.cn.md#%E8%B0%83%E8%8A%82%E7%AC%94%E8%AE%B0%E6%9C%AC%E4%BA%AE%E5%BA%A6) 已经包含了该驱动

#### USB
1.  通过hacktool查看USB信息,查找需要的端口,确定是USB2还是3,一些硬件需要改成内建以保证睡眠及唤醒的正常使用

hp15 usb2

hp21 摄像头内建

hp23 蓝牙内建

hs02 usb2

ss01 usb3  

2. 完成后导出kext安装到l/d以及ssdt文件复制到clover/acpi/patched，删除usbenjectall.kext 取消usb重命名补丁


3. 重启后,usb供电正常,usb3 速度5g


#### 屏蔽独显
> 如果使用了WhateverGreen.kext可以直接增加-wegnoegpu启动参数来屏蔽独显;也可以使用下面的方法从BIOS屏蔽独显.

1. 明白屏蔽独显的后果,如果是多系统比如Windows下也无法使用!如果你的DSDT包含_OFF方法建议使用补丁

2. 开机按 F2 进入 BIOS 界面，按下 Tab 和 Fn 组合键，接着按 Ctrl+Alt+Del 组合键重启。
3. 在开机画面继续按 F2 进入 BIOS 界面，此时 Advanced 和 Power 两个高级选项卡开启了。
4. 进入 Advanced 选项卡，在 Video Configuration 中将Special Features改为Disabled，将Graphic Mode由 switchable为Integrated ，最后将 PCI Express Graphic 改为 Disable 
5. 按 F10 保存 BIOS 设置重启
6. BIOS设置更改后在clover界面需要按F4重新生成oem DSDT
7. 查看系统信息-图形卡/显示器中没有nvidia显卡信息即屏蔽成功

#### 睡眠
* 屏蔽独显后，睡眠可用


加载原生电源管理[Native Power Management for Laptops](https://www.tonymacx86.com/threads/guide-native-power-management-for-laptops.175801/)后CPU变频可用,睡眠唤醒正常

### 其他
####  触控板 
1. 之前AppleSmartTouchPad经常遇到触控板失效的问题,升级到10.14后打算弃用,更换VoodooTouchPad
2. [syscl/OS-X-Voodoo-PS2-Controller](https://github.com/syscl/OS-X-Voodoo-PS2-Controller)最新版本添加了[Enable native/true multitouch gestures](https://github.com/syscl/OS-X-Voodoo-PS2-Controller/pull/10)
3. git到本地后使用命令make BITS=64自编译后安装kexts
* syscl的版本编译错误，直接使用rehabman的1.92
支持最多三指手势
需要在键盘-快捷键中找到想要实现的功能，在修改快捷键时直接三指滑动

#### 开启HIDPI
- [one-key-hidpi](https://github.com/xzhih/one-key-hidpi/blob/master/README-zh.md)

#### Mac导致Windows时间不同步
* sudo sh -c "$(curl -kfsSL https://raw.githubusercontent.com/hieplpvip/LocalTime-Toggle/master/fix_time_osx.sh)"

#### ssd trim
* sudo trimforce enable

#### 读取NTFS
* [fstab](https://wiki.archlinux.org/index.php/Fstab_(简体中文))
* [打开Mac OSX原生的读写NTFS功能](https://bbs.feng.com/read-htm-tid-9932031.html)
!!!注意加载NTFS需要关闭Windows下的快速启动,否则可能会造成数据丢失
建议使用label或者uuid加\挂载(推荐)
1. 编辑fstab sudo nano /etc/fstab
2. 写入 uuid=your-volume-uuid none ntfs rw,auto,nobrowse
3. 建立快捷方式 sudo ln -s /Volumes/Windows ~/Desktop/Windows

### TBD
1. 笔记本风扇转速显示
2. 开机第二阶段闪屏
3. 偶尔蓝牙固件升级失败导致蓝牙失效