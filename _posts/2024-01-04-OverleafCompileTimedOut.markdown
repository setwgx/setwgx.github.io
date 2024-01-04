---
layout: post
title: Overleaf Compile Timed Out
date: 2024-01-3 22:12:34 +0300
description: 解决 Overleaf 编译超时问题 # Add post description (optional)
img: /docker/cover.png # Add image post (optional)
tags: [Overleaf] # add tag
---

# 前言 
## Overleaf
Overleaf是一个在线的LaTex编辑器，相比于安装在本地的编译器更加方便快捷，不需要复杂的配置过程就可以直接在网页上进行LaTex的编写，支持版本控制、共享协助以及编译预览等功能，深受广大科研学者的喜爱。

![overleaf]({{site.baseurl}}/assets/img/overleafcompiletimedout/overleaf.png)

一般用户都具有一个Free Plan的版本，只具有符合需求的最基本功能，通常这个免费的版本可以符合大多数人的要求，但是也具有一些限制，比如只能有1人的协助人数，编译速度一般等，如果想要享受更多功能和特权则需要开通更高级的版本。

![upgrade]({{site.baseurl}}/assets/img/overleafcompiletimedout/upgrade.png)

Overleaf官网提供的个人付费版本包括259美元的标准版以及519美元的专业版，这对于一般用户来说是非常昂贵的。

因此，当在使用Overleaf的过程中，如果出现了部分限制，很多用户会选择各种方法来解决问题，下面介绍一下本人遇到的问题以及解决方法。

## 问题描述
最近在使用Overleaf进行科研论文写作的尝试，前面使用的时候一切正常，直到后面在实验部分添加了过多的图片，导致编译的过程中出现了以下的问题。

![error]({{site.baseurl}}/assets/img/overleafcompiletimedout/error.png)

### 尝试方法一：删除其他文件（失败）
由于我个人的项目中有三篇文件，因此我起初以为是文件数量超出了限制，导致无法进行编译，因此我删除了其他的文件，并没有因此解决问题。

### 尝试方法二：升级账户版本（失败。）
根据报错信息，我们可以知道，这个问题可以通过升级我们的个人账户版本解决。正如我前面提到的，官方提供的高级版本价格比较昂贵，对于基本功能已经满足的我们来说直接购买这并不划算。

因此我检索了Overleaf的高级版本的获取方法，直到看到大家都表明与IEEE collabratec关联的账户可以获取高级版本。

[IEEE collabratec免费升级overleaf高级账户](https://zhuanlan.zhihu.com/p/667619439)

[免费升级Overleaf Premium（IEEE Collabratec）](https://zhuanlan.zhihu.com/p/667078196)

[Overleaf升级Premium的白嫖计划](https://zhuanlan.zhihu.com/p/670574485)

不胜枚举，且检索到的结果只有这个方法，同质化内容严重。

通过根据教程尝试，发现目前这个方法已经失效。给官方发送邮件咨询得知，这个方法将不再适用，新的账户无法申请，旧的账户也将在一月份结束高级版本的使用。

### 尝试方法三：降低编译质量
定位问题在实验部分的图片上，由于之前使用的实验效果图使用的都是JPG或者是PNG格式的，占用资源较少。为了提高科研论文写作内容的质量，我使用了eps的图片格式，这类格式能同时包含矢量和点阵信息，还能包含文字，因此占用的资源较大。

![files]({{site.baseurl}}/assets/img/overleafcompiletimedout/files.png)

这是两个不同文件的对比。

因此，为了能够正常的完成这项工作，我选择将所有的eps图片还原成原来的PNG图片。

![before_eps]({{site.baseurl}}/assets/img/overleafcompiletimedout/before_eps.png)

![after_jpg]({{site.baseurl}}/assets/img/overleafcompiletimedout/after_jpg.png)

至此，问题解决。

如果在使用Overleaf的过程中出现了编译错误问题，可以从以上几个方向来排查错误并找到符合自己的解决方式。

<br />

## 参考资料
https://zhuanlan.zhihu.com/p/667619439

https://zhuanlan.zhihu.com/p/667078196

https://zhuanlan.zhihu.com/p/670574485