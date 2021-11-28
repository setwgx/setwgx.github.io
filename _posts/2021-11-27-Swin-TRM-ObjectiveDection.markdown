---
layout: post
title: Swin-Transformer for Object Detection :->(recurrence experiment)
date: 2021-11-27 10:54:22 +0300
description: Record the reproduction process of swin-transformer for object detection. # Add post description (optional)
img: /swin-TRM-Obective-Detection/0cover.png # Add image post (optional)
tags: [Ubuntu, Swin-Transformer, mmdetion] # add tag
---

## Preface
At the beginning of this year, swin-transformer achieved good results in multiple target tasks in the cv field. As the first recurring thesis experiment to record the process, I hope it can be helpful to other students.

This experiment was carried out in the Ubuntu 18.04 environment, the experiment code comes from the [official website](https://github.com/SwinTransformer/Swin-Transformer-Object-Detection).

The official website configuration can also be viewed [here](https://github.com/open-mmlab/mmdetection/blob/master/docs/get_started.md).

## Let's get started

#### Version environment introduction

The experimental computer uses two RTX 3090, CUDA 11.1, Driver Version 460.106.00.

The environment configuration is as follows:
```shell
ubuntu==18.04
python==3.8
pytorch==1.7.0
torchvision==0.8.1
mmcv-full==1.3.8
mmdet==2.11.0
apex==0.1
```

Compatible MMDetection and MMCV versions are shown as below. Please install the correct version of MMCV to avoid installation issues.

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

**Note:** You need to run `pip uninstall mmcv` first if you have mmcv installed.
If mmcv and mmcv-full are both installed, there will be `ModuleNotFoundError`.

#### Prepare environment
![photo1]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/1.png){:width="70%"}

![photo2]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/2.png){:width="70%"}

Create a conda virtual environment and activate it.
```shell
conda create -n swin-transformerv1 python=3.8 -y
conda activate swin-transformerv1
```

![photo3]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/3.png){:width="70%"}

Install PyTorch and torchvision following the official instructions.
```shell
conda install pytorch==1.7.0 torchvision==0.8.1 torchaudio==0.7.0 cudatoolkit=11.0 -c pytorch
```

#### Install MMDetection
![photo6]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/6.png){:width="70%"}

![photo8]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/8.png){:width="70%"}

It is recommended to install MMDetection with [MIM](https://github.com/open-mmlab/mim), which automatically handle the dependencies of OpenMMLab projects, including mmcv and other python packages.
```shell
pip install openmim
mim install mmdet
```
Or you can still install MMDetection manually:

You can simply install mmdetection with the following command:
```shell
pip install mmdet
```

or clone the repository and then install it:
```shell
git clone https://github.com/open-mmlab/mmdetection.git
cd mmdetection
pip install -r requirements/build.txt
pip install -v -e .  # or "python setup.py develop"
```
![photo18]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/18.png){:width="70%"}

Configure mmdet related content.
```shell
set MMCV_WITH_OPS = 1
pip install -e .
```

#### Install mmcv-full
![photo9]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/9.png){:width="70%"}

```shell
pip install mmcv-full -f https://download.openmmlab.com/mmcv/dist/{cu_version}/{torch_version}/index.html
```

Please replace {cu_version} and {torch_version} in the url to your desired one. For example, to install the latest mmcv-full with CUDA 11.0 and PyTorch 1.7.0, use the following command:
```shell
pip install mmcv-full==1.3.2 -f https://download.openmmlab.com/mmcv/dist/cu110/torch1.7.0/index.html
```

#### Install extra dependencies for Instaboost, Panoptic Segmentation, LVIS dataset, or Albumentations.
![photo10]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/10.png){:width="70%"}

![photo11]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/11.png){:width="70%"}

![photo12]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/12.png){:width="70%"}

![photo13]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/13.png){:width="70%"}

Install extra dependencies for Instaboost, Panoptic Segmentation, LVIS dataset, or Albumentations.
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

Run the program installation process.
```shell
python setup.py develop
```

Then download the official website pre-training model and corresponding config.

Here i downloaded :Swin-T (mask_rcnn)

mask_rcnn_swin_tiny_patch4_window7_mstrain_480-800_adamw_1x_coco.py

mask_rcnn_swin_tiny_patch4_window7_1x.pth

![photo20]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/20.png){:width="70%"}
```shell
#Single image reasoning
python demo/image_demo.py demo/demo.jpg configs/swin/mask_rcnn_swin_tiny_patch4_window7_mstrain_480-800_adamw_1x_coco.py ./checkpoints/mask_rcnn_swin_tiny_patch4_window7_1x.pth
```

#### apex installation
Apex is a deep learning acceleration library that can achieve hybrid precision training to ensure that compared with fully accurate training, the accuracy of specific tasks will not be lost.
```shell
git clone https://github.com/NVIDIA/apex
cd apex-master
#Compile and install
pip install -v --no-cache-dir --global-option="--cpp_ext" --global-option="--cuda_ext" .
```

Now that apex is also installed, let's carry out the experimental results of object detection.

![photo21]({{site.baseurl}}/assets/img/swin-TRM-Obective-Detection/21.png){:width="70%"}

Finally, there is a result of object detection related to the model. Here is the picture I downloaded from the Internet. After setting the corresponding path, you can view the result.

address：[https://arxiv.org/abs/2103.14030](https://arxiv.org/abs/2103.14030)

project address：[https://github.com/SwinTransformer/Swin-Transformer-Object-Detection](https://github.com/SwinTransformer/Swin-Transformer-Object-Detection)

mmcv documentation：[https://mmcv.readthedocs.io/en/latest/build.html](https://mmcv.readthedocs.io/en/latest/build.html)

apex：[https://github.com/NVIDIA/apex](https://github.com/NVIDIA/apex)
