---
layout: post
title: You-get+FFmpeg下载网络视频
date: 2021-11-22 11:06:58 +0300
description: 下载网络视频的方法。 # Add post description (optional)
img: /you-get+ffmpeg/0cover.png # Add image post (optional)
tags: [Windows, You-get, ffmpeg] # add tag
---

## 序
一直在很多视频平台上发现有趣的视频，希望能从平台上保存下来离线观看，但是平台提供的缓存或者下载方式还是有很多限制，让我们无法真正保存到本地。 所以我通过搜索发现，像you-get这样的python库可以帮助我们下载网页上的视频，并将下载的文件实际保存在我们的本地。 值得注意的是，you-get 只是一个下载器而不是破解器，您还可以下载包含会员的视频内容，但您需要有会员帐户。

FFmpeg 是一个开源计算机程序，可用于录制、转换数字音频和视频，并将其转换为流。 它为录制、转换和流式传输音频和视频提供了完整的解决方案，包括一个非常先进的音频/视频编解码器库 libavcodec。 为了保证高可移植性和编解码质量，libavcodec 中的许多代码都是从零开始开发的。 FFmpeg是在Linux平台下开发的，但也可以在其他操作系统环境下编译运行，包括Windows、Mac OS X等。

我的电脑环境是windows10，我用的是python3.8版本，浏览器是Firefox。 非会员和会员的下载方法是一样的，但是会员的下载要麻烦一些。 这里先讲简单的方法，再讲会员视频的下载方法。

## 开始

#### 非会员基本下载方法

![photo1]({{site.baseurl}}/assets/img/you-get+ffmpeg/1.png){:width="80%"}

这里我使用Anaconda配置了一个python3.8的虚拟环境，命名为swin，通过pip安装我们的python库you-get，这里我已经安装好了，所以说明已经安装了。

![photo2]({{site.baseurl}}/assets/img/you-get+ffmpeg/2.png){:width="80%"}

我们在这里使用来自 bilibili 的随机视频进行测试。 当然，这是一个非会员限制的视频。 我们找到了这个视频的网址。

![photo3]({{site.baseurl}}/assets/img/you-get+ffmpeg/3.png){:width="80%"}

文件的下载位置是当前路径位置，所以我们先把路径切换到我们要下载文件的位置，然后使用命令下载我们要下载的视频，格式为"you-get url"。 我们可以先使用 "-i" 参数来显示视频的可用分辨率，如上图所示。

![photo4]({{site.baseurl}}/assets/img/you-get+ffmpeg/4.png){:width="80%"}

接下来就是通过参数 "-format" 指定我们要下载的分辨率，这样我们就可以下载指定分辨率的视频了。

![photo5]({{site.baseurl}}/assets/img/you-get+ffmpeg/5.png){:width="80%"}

当然，如果你不关心清晰度或者没有选择，可以直接选择 "you-get url" 完成下载。

![photo6]({{site.baseurl}}/assets/img/you-get+ffmpeg/6.png){:width="80%"}

这是为非会员下载文件的一种方式。 当时，它只是一个视频下载器，而不是破解器。 下载会员视频时会出现这种现象。

没错，就是报错，告诉我们这是会员的视频，不能下载。

对于会员的视频，我们需要一些其他的辅助软件和视频平台的会员账号。

#### 会员基本下载方法
![photo7]({{site.baseurl}}/assets/img/you-get+ffmpeg/7.png){:width="80%"}

我们搜索ffmpeg，进入软件官网，选择我们的平台下载软件，我的操作系统是windows10。

![photo8]({{site.baseurl}}/assets/img/you-get+ffmpeg/8.png){:width="80%"}

选择你要下载的ffmpeg文件，箭头所指的软件。

![photo9]({{site.baseurl}}/assets/img/you-get+ffmpeg/9.png){:width="80%"}

在 github 上下载完整版。 下载完成后，我们不需要安装，解压即可使用，但需要将文件中bin的路径添加到环境变量Path中。

![photo10]({{site.baseurl}}/assets/img/you-get+ffmpeg/10.png){:width="80%"}

添加环境变量的方法不再赘述，按照步骤添加文件中bin所在的目录即可。

![photo11]({{site.baseurl}}/assets/img/you-get+ffmpeg/11.png){:width="80%"}

我们进入DOS命令行，输入ffmpeg试试看是否添加成功。 如果出现这些内容，说明ffmpeg的环境变量已经添加成功。

![photo12]({{site.baseurl}}/assets/img/you-get+ffmpeg/12.png){:width="80%"}

接下来，让我们尝试下载会员的内容。 因为我们需要使用cookies，而Firefox的cookie格式符合you-get中的格式要求，所以我们建议只使用Firefox进行此操作。

前往我们的视频平台网页并登录我们的会员账户。

![photo13]({{site.baseurl}}/assets/img/you-get+ffmpeg/13.png){:width="80%"}

Firefox 的默认 cookie 存放在该目录下, C:\Users\\[Username]\AppData\Roaming\Mozilla\Firefox\Profiles\brh0nlmh.default-release.

这里 [Username] 是你的计算机的用户名。

![photo14]({{site.baseurl}}/assets/img/you-get+ffmpeg/14.png){:width="80%"}

我们看到cookies.sqlite文件，里面存储了浏览器记录的cookie数据。 我们可以把这个文件复制到桌面，方便使用。

![photo15]({{site.baseurl}}/assets/img/you-get+ffmpeg/15.png){:width="80%"}

注意：如果没有找到AppData，记得勾选隐藏文件选项，这是一个隐藏文件夹。 然后去寻找它。

![photo16]({{site.baseurl}}/assets/img/you-get+ffmpeg/16.png){:width="80%"}

然后进入我们的会员视频，获取该会员视频的网址。

![photo17]({{site.baseurl}}/assets/img/you-get+ffmpeg/17.png){:width="80%"}

视频下载指令和非会员一样，"you-get url -c cookie.sqlite"。我们可以看到会员视频也可以下载成功了。

![photo18]({{site.baseurl}}/assets/img/you-get+ffmpeg/18.png){:width="80%"}

接下来，我们可以将视频内容离线保存到我们的电脑中，不受平台缓存或下载机制的限制。