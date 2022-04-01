---
layout: post
title: 谷歌账号无法登录问题
date: 2022-01-09 11:57:18 +0300
description: 解决退出Google账号后无法登录的问题。 # Add post description (optional)
img: /google-login/cover.png # Add image post (optional)
tags: [Google] # add tag
---

## 序

有时当我们在使用谷歌浏览器时退出我们的谷歌账户，我们就无法再次登录。 这个问题也困扰着很多人。 我通过查找资料找到了解决这个问题的方法，所以我把它放在这里供大家参考。

当你退出谷歌账号后，并再次尝试登录，可能会出现如下界面。

![photo1]({{site.baseurl}}/assets/img/google-login/0.png){:width="50%"}

网上有很多方法可以解决这个问题，但是我经过尝试后，发现只有这种方法对我有效。

我们找到chrome的安装路径。

![photo2]({{site.baseurl}}/assets/img/google-login/1.png){:width="60%"}

我们先关闭所有chrome进程，用这个exe打开浏览器，然后就可以正常登录Google账户了。