---
layout: post
title: HarmonyOS Next install Clash
date: 2025-01-5 22:25:16 +0300
description: Install Clash for HarmonyOS Next # Add post description (optional)
img: /HarmonyOSNextclash/cover.png # Add image post (optional)
tags: [HarmonyOS Next, Clash, HUAWEI] # add tag
---

# 前言 
## HarmonyOS Next系统
有幸成为HarmonyOS Next的公测用户，虽然国产手机系统还有诸多问题，但我相信国家和华为的能力。

## HarmonyOS
如果是HarmonyOS操作系统，可以直接下载Clash for Android。

## HarmonyOS Next
系统默认不允许侧载，使得Clash等代理软件无法安装。目前有两种方法可以使用。

### 出境易
目前HarmonyOS Next在出境易软件上架，可以先下载Chrome浏览器，在github上下载Clash for Android软件，使用出境易安装即可。

### 鸿蒙版本Clash Meta for Android (不建议)
Auto installer(小白鸿蒙开源自助餐) 是一款在Harmony Next安装hap应用的工具。

下载鸿蒙版本Clash Meta for Android软件，为一个hap安装包。

<!-- ![photo1]({{site.baseurl}}/assets/img/HarmonyOSNextclash/clashmeta.png) -->

下载并打开小白鸿蒙开源自助餐软件，让电脑和手机在同一网络下，开启手机开发者模式、USB调试和无线调试功能。

<!-- ![photo2]({{site.baseurl}}/assets/img/HarmonyOSNextclash/auto-installer.png) -->

在软件中输入无线调试的IP和端口，将hap安装包上传到应用。这个过程需要登录自己的华为账号。

<!-- ![photo3]({{site.baseurl}}/assets/img/HarmonyOSNextclash/xiaobaiHarmonyOS.png) -->

点击构建，将逐步完成安装环境、登陆检查和编译打包，完成后即可将Clash Meta安装到手机。

<!-- ![photo4]({{site.baseurl}}/assets/img/HarmonyOSNextclash/compile.png) -->

使用和Clash for Android方法一致，但目前该方法仍然为半成品，使用上体验不好，且必须持续开启开发者模式才行。

<br />

## 参考资料
https://blog.csdn.net/jdsjlzx/article/details/143560499

https://x.com/jichangtj/status/1848973981875114327?lang=en

https://jichanggo.com/harmonyos-clash

<!-- https://github.com/likuai2010/auto-installer -->

<!-- https://github.com/likuai2010/ClashMeta -->
