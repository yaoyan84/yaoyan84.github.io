---
layout: post
title:  "MacOS 10.12.3 for x86安装记录"
date:   2017-03-01 19:06:05
categories: 安装设置 操作系统
tags: hackintosh
excerpt: 在x86台式机上安装macos系统的过程。
---

## 1. 配置列表

| 项目 |   |
| --- | --- |
| CPU | intel i7 4790k |
| 主板技嘉 |  Z97X-UD3H-CF |
| 内存芝奇 | DDR3 1600MHz |
| 主硬盘 | 闪迪 SDSSDH2256G(256GB) |
| 显卡 | Nvidia GeForce GTX 770 (2GB／微星） |
| 声卡 | 板载 ALC1150 |
| 网卡| 板载 Intel Ethernet Connection i217-V |


>以上配置只有网卡和声卡装完后需要手动驱一下。

## 2. clover+10.12.3原版安装U盘制作
在bbs.pcbeta.com找到最新版本的osx 10.12.3的 clover+原版安装镜像的下载:

传送门：[macOS Sierra 10.12.3 With Clover 3974原版dmg镜像带Clover 3974](http://bbs.pcbeta.com/viewthread-1731962-1-3.html)

使用transmac将dmg镜像还原至U盘中。

>i217网卡默认不能驱，所以要先准备好网卡的kext和相应工具，避免完全安装系统后，无法上网找工具。

kext工具： [Kext Utility](https://github.com/yaoyan84/Hackintosh/blob/master/TOOLS/Kext%20Utility.app.zip)
网卡驱动： [Intel Ethernet I217-V](https://github.com/yaoyan84/Hackintosh/tree/master/PC%20Z97X-UD3H/Intel%20Ethernet%20I217-V) 里的AppleIntelE1000e.kext。

## 3. 安装过程
- 直接使用U盘引导，进入系统安装界面。GTX770完美支持，安装界面时双显示器都亮。
- 使用磁盘工具，选择SSD，然后“抹掉”，创建GUID分区图的MacOS扩展（日志式）磁盘分区。
- 然后将系统安装到新建的SSD盘中。
- 过程中第一次进度条走完，重启后还会走一遍进度条。整个过程保持插入U盘，==并且系统安装完后，在安装好Clover之前依然要依赖U盘来引导系统==。

## 4. 安装网卡驱动
拿到AppleIntelE1000e.kext，使用kext工具安装网卡驱动，重启后网卡识别即可上网。

## 5. 安装Clover引导
### 5.1. 安装Clover

打开[Clover EFI bootloader](https://sourceforge.net/projects/cloverefiboot/)页面，当前最新版本为 Clover_v2.4k_r4012.zip。
>因为安装是完全抹掉SSD为GUID分区，主板也是开了EFI，所以安装过程中要选择“自选”，勾选EFI相关安装选项。

### 5.2. 拷贝Clover配置文件
1. 使用工具 [Clover Configurator 4.38.2](https://github.com/yaoyan84/Hackintosh/blob/master/TOOLS/Clover%20Configurator%204.38.2.zip) 挂载EFI分区，安装U盘的EFI和SSD的EFI都要挂载；
2. 将安装U盘里的Clover的配置文件和必要的kext补丁包复制到SSD相同的EFI相同位置

此时，卸载EFI分区，可以拔掉U盘，已可以使用SSD上的Clover启动系统了，重启验证一次，并在启动时，在Clover选项界面选择Option，找到声卡选项，先确认一下我们的ALC1150声卡的ID值，本套装备显卡带有HDMI音频输出，所以ALC1150的ID是==2==。**该值驱动声卡时需要**。

## 6. 驱动声卡，并开启5.1声道
### 6.1. 声卡驱动安装

>驱动安装方法参考：https://github.com/yaoyan84/audio_CloverALC

1. 从Github上下载脚本： [audio_cloverALC-120.command.zip](https://github.com/yaoyan84/audio_CloverALC/blob/master/deprecated/audio_cloverALC-120.command.zip) 解压得 audio_cloverALC-120_v1.0b0.command
2. 使用 Clover Configurator 挂载SSD的EFI分区，并加载当前的config文件，在设备里找到声卡注入设置，输入值“2”保存退出。
3. 因为Clover Configurator挂在EFI的挂在点，与脚本要求不符合，所以运行会一直提示没有挂载EFI。解压后的脚本audio_cloverALC-120_v1.0b0.command，第56行：

```
gCloverDirectory=/Volumes/$gStartupDisk/EFI/CLOVER
```
改为

```
gCloverDirectory=/Volumes/EFI/CLOVER
```

4. 运行脚本开始自动安装声卡，修改clover配置注入相关信息。
 * 问题1：驱动依赖于原版的AppleHDA.kext包，网上很多分享；
 * 问题2: 提示找不到codes，发现在EFI区里的clover里有外挂了AppleHDADisable的包，导致系统不会加载声卡，所以无法发现codes，删掉后重启再试即可正常安装。

安装完成后，重启系统，声音输出设备选择“内置扬声器”，即可以发声，无电流声等杂音，完美驱动。

### 6.2 立体声5.1音箱设置
1. 在Lanchpad的“其他”里找到“音频MIDI设置”，添加一个“聚合设备”勾选**内建输出**、和两个**内建线路输出**
2. 配置扬声器，配置选择为“5.1环绕声”，并勾选3个流
3. 按照正确的音箱线的颜色插的话，测试只有左前、右前两个音箱出声，经多次换线测试确认，要将黑线插入蓝孔(linein)，黄线插入红孔(麦克风in)，中置音箱和左右环绕才能正常出声。

选择该**聚集设备**为输出设备，经5.1声道测试片源测试环绕立体声已经出来了。可在**音频MIDI设置**中对各设备的输出增益进行调整来达到最佳效果。