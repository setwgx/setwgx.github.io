---
layout: post
title: Stable Diffusion WebUI
date: 2024-01-26 11:51:19 +0300
description: 在 Windows 本地部署 Stable Diffusion WebUI # Add post description (optional)
img: /stablediffusionwebui/cover.png # Add image post (optional)
tags: [Stable Diffusion WebUI, AI, Application, Windows] # add tag
---

<!-- ![stable_diffusion]({{site.baseurl}}/assets/img/stablediffusiondeploy/00stable_diffusion2.png){:width="70%"} -->

# 前言 
前面个人尝试了 Stable Diffusion v2 模型在 DOCKER 上的部署，也尝试过在 VMWare 上安装虚拟机进行部署，无一例外都因为虚拟机环境和显卡之间的交互带来了很多的麻烦。

这次我直接在本机上进行模型部署，记录一下本地化部署 Stable Di发fusion WebUI 的整体流程。

本地化部署的优点在于生成数量不受限制，自由度高并且使用方便，适合个性化设置。缺点则在于需要一定的硬件设备。

本机的软硬件配置：

- Win11 OS
- NVIDIA RTX 3060 6G
- Python 3.10.6
- torch 2.0.0+cu118
- torchvision 0.15.1+cu118

安装的CUDA版本如下：

![01nvidia-smi]({{site.baseurl}}/assets/img/stablediffusionwebui/01nvidia-smi.png){:width="70%"}

## 本地化部署流程
个人主机基本资源配置在本文不再赘述，Windows 环境下的配置下载过程与其他系统没有区别，主要在于安装过程，这些可以在网上找到很多的教程。

我在本地的 Anaconda3 上配置实验环境，创建时 Python 指定版本 3.10.6。同时在本机上下载配置 Git 环境。

![02创建环境]({{site.baseurl}}/assets/img/stablediffusionwebui/02创建环境.png){:width="70%"}

通过 git 指令或者网页下载 stable-diffusion-webui 项目，我这里下载的最初 1.1.0 版本, 正常情况下载最新版本即可。``` git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git ```

![03项目代码]({{site.baseurl}}/assets/img/stablediffusionwebui/03项目代码.png){:width="70%"}

官网下载基础模型，stable-diffusion-webui 至少需要使用一个才能进行 AI 画图，这个项目的权重模型后缀通常是 .ckpt 或者 .safetensors。可以去 Stable-Diffusion-V2 下载或者其他提供权重模型的网站，这也是个性化模型的一部分。

![04sdv2模型权重下载]({{site.baseurl}}/assets/img/stablediffusionwebui/04sdv2模型权重下载.png){:width="70%"}

下面是部分常见的模型权重也可以自行寻找。

- Stable Diffusion v2.1，简称SD，是CompVis与合作团队最初发布的模型，不断更新中。网络上很多模型都基于此模型训练而来。适合画真人、动物、自然、科技、建筑的图像，也学习过历史上许多画家的画风。

- Chilloutmix，写实风格的模型，适合写出2.5次元，融合日韩真人与动漫风格的图像。

- Deliberate，基于SD-1.5模型，适合绘制精致写实风格的人物、动物、自然风景。

- Realistic Vision v1.4，写实风人物与动物模型。

- OrangeMix3，混合多种风格的动漫绘图模型，偏写实。

[ChilloutMix](https://link.zhihu.com/?target=https%3A//civitai.com/models/6424/chilloutmix)

[lora](https://huggingface.co/blog/lora)

[Civitai](https://civitai.com/)

[四比三备份站](https://4B3.com/)

将下载的权重模型放在项目目录 ```models/Stable-diffsuion``` 下，如图我下载了基本模型权重、 Stable-Diffusion 版本 2 的基本权重以及 Chilloutmix 的权重模型。

![05模型权重]({{site.baseurl}}/assets/img/stablediffusionwebui/05模型权重.png){:width="70%"}

打开项目目录，用记事本打开 webui-user.bat 文件，将以下代码 ```set COMMANDLINE_ARGS=--medvram --opt-split-attention --xformers``` 添加到 COMMANDLINE_ARGS 参数里面。

![06webui-user]({{site.baseurl}}/assets/img/stablediffusionwebui/06webui-user.png){:width="70%"}

保存后运行该批程序处理文件，运行的时候设备会自动下载所需依赖，报错后解决问题再运行文件，直到 stable-diffusion-webui 项目可以正常运行起来。

![07运行项目]({{site.baseurl}}/assets/img/stablediffusionwebui/07运行项目.png){:width="70%"}

这样子模型就是运行起来了。

ERROR one：如果运行后前端界面报错 Something went wrong Expecting value: line 1 column 1 (char 0)   stable-diffusion-webui ，这一般是国内开启代理造成的，关闭代理即可。

ERROR two：如果运行时报错 TypeError: AsyncConnectionPool.\_\_init\_\_(), new install bug, httpx==0.24.1 ， 则输入指令 ```venv\Scripts\activate``` 激活后再次尝试。

项目运行起来后，默认端口为 7860，我们在本机的浏览器上访问 ```localhost:7860``` 即可进入 webui 的界面。

![08本地部署界面]({{site.baseurl}}/assets/img/stablediffusionwebui/08本地部署界面.png){:width="70%"}

我们找个 prompt 进行一下测试，可以正常使用。

![09本地部署测试]({{site.baseurl}}/assets/img/stablediffusionwebui/09本地部署测试.png){:width="70%"}

这只是简单的测试，如果需要更多更好的内容和生成的结果，则需要我们对参数进行合理的调整，基本可以满足日常的使用。

如果需要更多的功能或者特定的风格，我们则需要进行针对性的训练得到符合自己需求的权重模型，具体应用在后续再补充。

<br />

## 参考资料
[MidJourney平替——本地化部署Stable Diffusion WebUI](https://zhuanlan.zhihu.com/p/623079275)

[TypeError: AsyncConnectionPool.\_\_init\_\_(), new install bug, httpx==0.24.1](https://github.com/AUTOMATIC1111/stable-diffusion-webui/issues/13840)

[Bug: "Expecting value: line 1 column 1 (char 0)"](https://github.com/AUTOMATIC1111/stable-diffusion-webui/issues/9132)

[Stable Diffusion+chilloutmix+koreanDollLikeness](https://zhuanlan.zhihu.com/p/622518653)

[ChilloutMix 下载/使用/效果图](https://zhuanlan.zhihu.com/p/632997666)

[stable-diffusion-webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui/releases?page=2)