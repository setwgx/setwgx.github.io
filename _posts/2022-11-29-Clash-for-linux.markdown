---
layout: post
title: Clash for linux
date: 2022-11-29 08:28:22 +0300
description: 在Linux系统上安装代理的流程记录 # Add post description (optional)
img: /clashforlinux/cover.png # Add image post (optional)
tags: [Linux, Clash] # add tag
---

## 序
当前，有很多工作和学习常用的网站因其服务器不在国内导致访问不稳定，由此带来的是实验用机经常因网络问题出现不可控的现象，十分影响使用体验，因此这里记录下在linux图形化界面安装代理的全过程，以便未来查验。

### 下载解压重命名和启动

在众多代理软件中，个人最喜欢使用的是CLASH，它为不同的操作系统都做了适配，包括但不限于WINDOWS、LINUX、MACOS等。因此这里也是下载CLASH软件用于LINUX系统。

```
# 下载CLASH软件包
wget https://github.com/Fndroid/clash_for_windows_pkg/releases/download/0.18.8/Clash.for.Windows-0.18.8-x64-linux.tar.gz
# 解压CLASH软件包
tar -xvf Clash.for.Windows-0.18.8-x64-linux.tar.gz
# 重命名文件夹为CLASH
mv 'Clash for Windows-0.18.8-x64-linux'  clash
```

把文件放在你需要的地方，我这里放在~/Desktop/softwares路径下。

![photo1]({{site.baseurl}}/assets/img/clashforlinux/installionpath.png)

这个时候CLASH软件已经下载安装完成了，打开文件夹，里面的cfw就是我们软件的启动文件，双击cfw打开CLASH。

![photo2]({{site.baseurl}}/assets/img/clashforlinux/program.png)

### 设置开机自启

打开cfw程序后我们看到这样的一个界面，我们要为程序设置开机自启，方便以后每次开机自动设置网络代理。

开启Start with Linux选项。

![photo3]({{site.baseurl}}/assets/img/clashforlinux/clash.png)

### 下载订阅

选择左侧的Profiles，在这里输入我们的订阅链接，然后点击Download下载。

成功即可选择我们的节点。

![photo6]({{site.baseurl}}/assets/img/clashforlinux/subscription.png)

### 设置https/http代表为本地的7890端口

在配置之前我们一定要先开启CLASH里面的Allow LAN的选项。

![photo4]({{site.baseurl}}/assets/img/clashforlinux/allowLan.png)

然后设置本地代理。

![photo5]({{site.baseurl}}/assets/img/clashforlinux/systemproxy.png)

(上面要一样,Ignore Hosts输入这个，localhost,127.0.0.0/8,;;1)

注意：如果你加入了订阅，但还是无法访问wei网，可以考虑重启一下。

### 完毕

此时在LINUX系统上安装CLASH用于代理的流程就完毕了。

每次我们使用代理的时候只需要开启Allow LAN并且设置Network Proxy为Manual即可。

![photo7]({{site.baseurl}}/assets/img/clashforlinux/useproxy.png)

