---
layout: post
title: Win11InstallAPK
date: 2025-05-06 22:39:45 +0300
description: The Way to install apk in Windows 11 # Add post description (optional)
img: /Win11_Install_Apk/cover.png # Add image post (optional)
tags: [Windos11, APK, HyperV] # add tag
---

# 前言 
微软和谷歌合作推出用于安装APK的安卓子系统，相比于各种虚拟机来说会更加流畅。

# 安装教程

## 基本环境及硬件需求
- 操作系统：WINDOWS11
- 内存：至少8GB
- 处理器：英特尔酷睿I3 8th、AMD Ryzen3000及以上
- 存储：符合WSA子系统的存储空间(5GB以上，不含应用存储大小)

## 开启虚拟化功能

安卓子系统属于虚拟化功能，需要执行 WINDOWS 开启虚拟化功能。打开菜单->输入控制面板->点击程序->启用或关闭windows功能->勾选HyperV。

完成后需要重启电脑以完成虚拟化功能更改的配置。

后续 Windows Subsystem for Android 需要下载 AMAZON APPSTORE，因此我们同时调整电脑系统的地区为美国，如下图所示。

![png1]({{site.baseurl}}/assets/img/Win11_Install_Apk/HyperVandRegion.png){:width="70%"}  

## 下载并安装WSA

打开 WINDOWS 应用商店，输入Windows Subsystem for Android，选择 APK File Install For Windows 软件，等待应用下载。

由于我的电脑已经安装了其他的 WSA 应用，如有虚拟化相关的报错请自行解决。

![png2]({{site.baseurl}}/assets/img/Win11_Install_Apk/DownloadAPKFileInstallForWindows.png){:width="70%"}  

## WSA连接ADB

下载完成后运行会执行连接 ADB 的步骤，按照安装界面提示点击高级设置，开启开发者模式。

![png3]({{site.baseurl}}/assets/img/Win11_Install_Apk/APKFileInstallForWindows.png){:width="70%"}  

成功连接ADB后，我们点击菜单中新装的 Windows Subsystem for Android 应用，打开后我们就可以看到如下界面，这说明我们的 WAS 已经安装完毕。

![png4]({{site.baseurl}}/assets/img/Win11_Install_Apk/WindowsSubsystemForAndroidTM.png){:width="70%"}  

## WSA安装软件

进入软件后，我们就可以安装 APK 并运行尝试了。这里我下载了个游戏软件用于测试。

打开系统中 APK File Install For Windows 软件，选择安装 Install APK File，浏览我们刚刚下载的软件，等待安装完毕。

![png5]({{site.baseurl}}/assets/img/Win11_Install_Apk/ConnectToADB.png){:width="70%"}  

## 运行软件

安装完毕后选择 APPs，可以看到刚刚安装的软件，选择更多 Open 就可以运行软件。经过测试发现软件运行流畅，GREAT。

![png6]({{site.baseurl}}/assets/img/Win11_Install_Apk/Result.png){:width="70%"} 


<br />

## 参考资料
[WIN11原生运行安卓](https://post.smzdm.com/p/a6pp8vp0/)
