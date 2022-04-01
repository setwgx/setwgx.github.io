---
layout: post
title: 用于物体检测的 Swin-Transformer
date: 2021-11-27 10:54:22 +0300
description: 记录swin-transformer用于目标检测的复现过程。 # Add post description (optional)
img: /swin-TRM-Obective-Detection/0cover.png # Add image post (optional)
tags: [Ubuntu, Swin-Transformer, mmdetion] # add tag
---

## 序
今年年初，swin-transformer 在 cv 领域的多个目标任务中取得了不错的成绩。 作为我第一个要复现的论文实验来记录这个过程，希望对其他同学有所帮助。

本实验在 Ubuntu 18.04 环境下进行，实验代码来自[官网网站](https://github.com/SwinTransformer/Swin-Transformer-Object-Detection).

官网配置也可以在[这里](https://github.com/open-mmlab/mmdetection/blob/master/docs/get_started.md)查看。

## 开始

#### 版本环境介绍

实验电脑使用两台RTX 3090，CUDA 11.1，驱动版本460.106.00。

环境配置如下：
```shell
ubuntu==18.04
python==3.8
pytorch==1.7.0
torchvision==0.8.1
mmcv-full==1.3.8
mmdet==2.11.0
apex==0.1
```

兼容的 MMDetection 和 MMCV 版本如下所示。 请安装正确版本的 MMCV 以避免安装问题。 

| MMDetection version |    MMCV version     |
|:-------------------:|:-------------------:|
| master              | mmcv-full>=1.3.14, <1.4.0 |
| 2.18.0              | mmcv-full>=1.3.14, <1.4.0 |
| 2.17.0              | mmcv-full>=1.3.14, <1.4.0 |
| 2.16.0              | mmcv-full>=1.3.8, <1.4.0 |
| 2.15.1              | mmcv-full>=1.3.8, <1.4.0 |
| 2.15.0              | mmcv-full>=1.3.8, <1.4.0 |
| 2.14.0              | mmcv-full>=1.3.8, <1.4.0 |
| 2.13.0              | mmcv-full>=1.3.3, <1.4.0 |
| 2.12.0              | mmcv-full>=1.3.3, <1.4.0 |
| 2.11.0              | mmcv-full>=1.2.4, <1.4.0 |
| 2.10.0              | mmcv-full>=1.2.4, <1.4.0 |
| 2.9.0               | mmcv-full>=1.2.4, <1.4.0 |
| 2.8.0               | mmcv-full>=1.2.4, <1.4.0 |
| 2.7.0               | mmcv-full>=1.1.5, <1.4.0 |
| 2.6.0               | mmcv-full>=1.1.5, <1.4.0 |
| 2.5.0               | mmcv-full>=1.1.5, <1.4.0 |
| 2.4.0               | mmcv-full>=1.1.1, <1.4.0 |
| 2.3.0               | mmcv-full==1.0.5    |
| 2.3.0rc0            | mmcv-full>=1.0.2    |
| 2.2.1               | mmcv==0.6.2         |
| 2.2.0               | mmcv==0.6.2         |
| 2.1.0               | mmcv>=0.5.9, <=0.6.1|
| 2.0.0               | mmcv>=0.5.1, <=0.5.8|

**注意：** 如果你安装了 mmcv，则需要先运行 `pip uninstall mmcv`。
如果同时安装了 mmcv 和 mmcv-full，则会出现 `ModuleNotFoundError`。 

#### 环境配置
![photo1]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/1.png){:width="70%"}

![photo2]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/2.png){:width="70%"}

创建一个 conda 虚拟环境并激活它。
```shell
conda create -n swin-transformerv1 python=3.8 -y
conda activate swin-transformerv1
```

![photo3]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/3.png){:width="70%"}

按照官方说明安装 PyTorch 和 torchvision。
```shell
conda install pytorch==1.7.0 torchvision==0.8.1 torchaudio==0.7.0 cudatoolkit=11.0 -c pytorch
```

#### 安装 MMDetection
![photo6]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/6.png){:width="70%"}

![photo8]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/8.png){:width="70%"}

建议通过 [MIM](https://github.com/open-mmlab/mim) 安装 MMDetection, 它可以自动处理 OpenMMLab 项目的依赖关系，包括 mmcv 和其他 python 包。
```shell
pip install openmim
mim install mmdet
```
或者您仍然可以手动安装 MMDetection：

您可以使用以下命令简单地安装 mmdetection：
```shell
pip install mmdet
```

或下载clone存储库，然后安装它：
```shell
git clone https://github.com/open-mmlab/mmdetection.git
cd mmdetection
pip install -r requirements/build.txt
pip install -v -e .  # or "python setup.py develop"
```
![photo18]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/18.png){:width="70%"}

配置 mmdet 相关内容。
```shell
set MMCV_WITH_OPS = 1
pip install -e .
```

#### 安装 mmcv-full
![photo9]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/9.png){:width="70%"}

```shell
pip install mmcv-full -f https://download.openmmlab.com/mmcv/dist/{cu_version}/{torch_version}/index.html
```

请将网址中的 {cu_version} 和 {torch_version} 替换为您想要的。 例如，要使用 CUDA 11.0 和 PyTorch 1.7.0 安装最新的 mmcv-full，请使用以下命令：
```shell
pip install mmcv-full==1.3.2 -f https://download.openmmlab.com/mmcv/dist/cu110/torch1.7.0/index.html
```

#### 为 Instaboost、Panoptic Segmentation、LVIS 数据集或 Albumentations 安装额外的依赖项。
![photo10]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/10.png){:width="70%"}

![photo11]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/11.png){:width="70%"}

![photo12]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/12.png){:width="70%"}

![photo13]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/13.png){:width="70%"}

为 Instaboost、Panoptic Segmentation、LVIS 数据集或 Albumentations 安装额外的依赖项。
```shell
# for instaboost
pip install instaboostfast
# for panoptic segmentation
pip install git+https://github.com/cocodataset/panopticapi.git
# for LVIS dataset
pip install git+https://github.com/lvis-dataset/lvis-api.git
# for albumentations
pip install albumentations>=0.3.2 --no-binary imgaug,albumentations
```

![photo17]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/17.png){:width="70%"}

运行程序进行安装过程。
```shell
python setup.py develop
```

然后下载官网预训练模型和相应的config。

我在这里下载 :Swin-T (mask_rcnn)

mask_rcnn_swin_tiny_patch4_window7_mstrain_480-800_adamw_1x_coco.py

mask_rcnn_swin_tiny_patch4_window7_1x.pth

![photo20]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/20.png){:width="70%"}
```shell
#Single image reasoning
python demo/image_demo.py demo/demo.jpg configs/swin/mask_rcnn_swin_tiny_patch4_window7_mstrain_480-800_adamw_1x_coco.py ./checkpoints/mask_rcnn_swin_tiny_patch4_window7_1x.pth
```

#### 安装 apex
Apex 是一个深度学习加速库，可以实现混合精度训练，确保与完全准确训练相比，特定任务的准确性不会丢失。
```shell
git clone https://github.com/NVIDIA/apex
cd apex-master
#Compile and install
pip install -v --no-cache-dir --global-option="--cpp_ext" --global-option="--cuda_ext" .
```

现在也安装了apex，我们来进行物体检测的实验测试。

![photo21]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/21.png){:width="70%"}

最后，还有一个与模型相关的物体检测结果。 这是我从网上下载的图片。 设置好对应的路径后，就可以查看结果了。 

<br />

原文地址：[https://arxiv.org/abs/2103.14030](https://arxiv.org/abs/2103.14030)

项目地址：[https://github.com/SwinTransformer/Swin-Transformer-Object-Detection](https://github.com/SwinTransformer/Swin-Transformer-Object-Detection)

mmcv文档：[https://mmcv.readthedocs.io/en/latest/build.html](https://mmcv.readthedocs.io/en/latest/build.html)

apex：[https://github.com/NVIDIA/apex](https://github.com/NVIDIA/apex)