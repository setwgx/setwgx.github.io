---
layout: post
title: Megengine平台图像降噪比赛(无名次)
date: 2022-04-06 10:51:57 +0300
description: 参加Megengine平台的降噪比赛baseline运行记录 # Add post description (optional)
img: /MegengineDenoisingCompetition/pic0.png # Add image post (optional)
tags: [Megengine, Denoise, Competition] # add tag
---

## 序
第一次参加平台举办的竞赛活动，由于时间紧迫且竞赛内容不属于我的研究方向，抱着众在参与的心态进行了一次尝试，学习一下这种平台举办竞赛活动的流程以及方法。之后会尝试参加一些自己的研究方向上比赛。

这里仅记录这次比赛的流程和Megengine平台的使用方法。

### 题目：小模型盲降噪

本次比赛中要完成的任务是对一个 RAW 格式的图像进行降噪。所谓 “RAW” 格式，即一般认为是没有进行 ISP 进一步处理的、接近图像传感器直接读取值的数据。

每一个输入的RAW数据都是一个 256 x 256 的 uint16。 你不知道这个 RAW 数据是用什么传感器、在什么条件下进行拍摄的。这些数据被预处理为 RGGB 的排列方式以及 0~65535 的取值范围。

针对每个输入，输出一个同样大小的 256x256 的 uint16 图像，表示降噪后的结果。这里所说的“降噪”，指：以零为均值的，在多次拍摄中变化的噪声（如，读取噪声，热噪声，等）。

我们会提供训练用的成对的数据集，以及用于评估你的算法性能的测试集。

你所有的测试图都需要是用一个模型跑出来的，并且这个模型拥有不超过 100K 个参数。

比赛最后会通过审核源代码的方式来检查选手的提交是否能够满足以上的一致性和参数量的要求。

#### 比赛数据
本次比赛平台提供 比赛数据集 数据集将在初赛开始后，在平台进行公开，报名期间将无权限查看数据集，请在初赛开始后，进入比赛项目，点击编辑，添加此数据集，即可在项目内使用此数据集。

##### 训练集：
数据集包含 8192 对 256 x 256 的图像，存储为两个文件，分别表示输入、参考输出。

每个文件是顺序存储的 8192 个图像，每个图像直接用 256 x 256 个 uint16 来表示（行优先存储）。故训练集就是 2 个分别为 8192 x 256 x 256 x 2 字节大的二进制文件。

##### 测试集：
数据集包含 1024 个 256 x 256 的图像，用同样的格式存储。

你需要提交的就是一个和测试集的格式、文件大小相同的另一个文件，来表示你的降噪后结果。

#### 提交要求
测试集上的输出，1024 个 patch 上依次的结果，提交一个大的 .bin 文件。如果想得奖金，要求至少把 inference 的代码和模型文件开源。

#### 评分标准
我们同样准备了对应测试集的“参考输出”。对于每一个测试图像，我们会计算你提交的结果与参考输出的差的绝对值，在图像内取平均之后除以参考输出图像的平均像素值的平方根，来作为对这张测试图像的差距值。之后我们把所有图像上的差距值取平均，用 100 除之后取以 10 为底的对数，然后乘以 5 作为最终的“得分”。 详细计算得分的逻辑可以看我们提供给你的计算分数的脚本。

## 环境配置及Baseline说明

| 实验环境 |
|:-------------------:|
| MegEngine1.8 |
| Python 3.7 |
| Jupter Notebook |

##### 网络定义
我们使用 MegEngine 来示例如何求解这个问题。 为简便，我们使用一个 6 层卷积组成的小网络。

导入相关的包和基本设置
```
import megengine as mge
import megengine.module as M
import megengine.functional as F

patchsz = 256
batchsz = 16
```

定义网络结构
```
class Predictor(M.Module):
    def __init__(self):
        super().__init__()
        self.conv1 = M.Sequential(
            M.Conv2d(4, 50, 3, padding = 1, bias = True),
            M.LeakyReLU(negative_slope = 0.125),
            M.Conv2d(50, 50, 3, padding = 1, bias = True),
            M.LeakyReLU(negative_slope = 0.125),
        )
        self.conv2 = M.Sequential(
            M.Conv2d(50, 50, 3, padding = 1, bias = True),
            M.LeakyReLU(negative_slope = 0.125),
            M.Conv2d(50, 50, 3, padding = 1, bias = True),
            M.LeakyReLU(negative_slope = 0.125),
        )
        self.conv3 = M.Sequential(
            M.Conv2d(50, 50, 3, padding = 1, bias = True),
            M.LeakyReLU(negative_slope = 0.125),
            M.Conv2d(50, 4, 3, padding = 1, bias = True),
            M.LeakyReLU(negative_slope = 0.125),
        )
    def forward(self, x):
        n, c, h, w = x.shape
        x = x.reshape((n, c, h // 2, 2, w // 2, 2)).transpose((0, 1, 3, 5, 2, 4)).reshape((n, c * 4, h // 2, w // 2))
        x = self.conv1(x)
        x = self.conv2(x)
        x = self.conv3(x)
        x = x.reshape((n, c, 2, 2, h // 2, w // 2)).transpose((0, 1, 4, 2, 5, 3)).reshape((n, c, h, w))
        return x
```

##### 参数量检查
在运行之前，我们需要检查一下是否参数量符合标准。MegEngine 有一个 module_stats 的函数正好可以用来干这件事情。 我们只需要关注 param_dims 这个值是否超 100K 即可。
```
if __name__ == '__main__':
    
    import random
    import numpy as np
    from megengine.utils.module_stats import module_stats

    net = Predictor()

    input_data = np.random.rand(1, 1, 256, 256).astype("float32")
    total_stats, stats_details = module_stats(
        net,
        inputs = (input_data,),
        cal_params = True,
        cal_flops = True,
        logging_to_stdout = True,
    )

    print("params %.3fK MAC/pixel %.0f"%(total_stats.param_dims/1e3, total_stats.flops/input_data.shape[2]/input_data.shape[3]))
```

##### 训练数据加载
数据可以直接通过 frombuffer 加载到内存里。因为总数据量不大，就在内存一直放着就行了。
```
print('loading data')
    content = open('dataset/burst_raw/competition_train_input.0.2.bin', 'rb').read()
    samples_ref = np.frombuffer(content, dtype = 'uint16').reshape((-1,256,256))
    content = open('dataset/burst_raw/competition_train_gt.0.2.bin', 'rb').read()
    samples_gt = np.frombuffer(content, dtype = 'uint16').reshape((-1,256,256))
```

##### 训练
使用 Adam 进行训练。为简单起见，示例里只跑 20000 步。你一般应当跑更久，并定期保存模型。
```
train_steps = 20000
    opt = mge.optimizer.Adam(net.parameters(), lr = 1e-3)
    gm = mge.autodiff.GradManager().attach(net.parameters())

    losses = []

    rnd = random.Random(100)
    
    print('training')

    for it in range(0, train_steps):
        for g in opt.param_groups:
            g['lr'] = 2e-4 * (train_steps - it) / train_steps
            
        opt.zero_grad()
        
        batch_inp_np = np.zeros((batchsz, 1, patchsz, patchsz), dtype = 'float32')
        batch_out_np = np.zeros((batchsz, 1, patchsz, patchsz), dtype = 'float32')

        for i in range(batchsz):
            idx = rnd.randrange(len(samples_ref))
            batch_inp_np[i, 0, :, :] = np.float32(samples_ref[i, :, :]) * np.float32(1 / 65536)
            batch_out_np[i, 0, :, :] = np.float32(samples_gt[i, :, :]) * np.float32(1 / 65536)

        batch_inp = mge.tensor(batch_inp_np)
        batch_out = mge.tensor(batch_out_np)

        with gm:
            pred = net(batch_inp)
            loss = F.abs(pred - batch_out).mean()
            gm.backward(loss)
            opt.step().clear_grad()

        loss = float(loss.numpy())
        losses.append(loss)
        if it % 10 == 0:
            print('it', it, 'loss', loss, 'mean', np.mean(losses[-100:]))

    fout = open('model','wb')
    mge.save(net.state_dict(), fout)
    fout.close()
```

##### 推理
读取测试集，进行推理，把结果写到文件里。
```
print('prediction')

    content = open('dataset/burst_raw/competition_test_input.0.2.bin', 'rb').read()
    samples_ref = np.frombuffer(content, dtype = 'uint16').reshape((-1,256,256))
    fout = open('workspace/competition_prediction.0.2.bin', 'wb')

    import tqdm

    for i in tqdm.tqdm(range(0, len(samples_ref), batchsz)):
        i_end = min(i + batchsz, len(samples_ref))
        batch_inp = mge.tensor(np.float32(samples_ref[i:i_end, None, :, :]) * np.float32(1 / 65536))
        pred = net(batch_inp)
        pred = (pred.numpy()[:, 0, :, :] * 65536).clip(0, 65535).astype('uint16')
        fout.write(pred.tobytes())

    fout.close()
```

至此，有关这个小去噪模型的baseline就可以正常运行，并且通过训练得到一个model，通过测试得到一个result.0.2.bin的一个二进制文件，只需要按照题目需求，将得到的bin文件修改名称，放置在submit文件夹内即可，完整路径为/workspace/submit/result.bin。

提交后可以等待系统判定结果，这个baseline的成绩如下。

![photo1]({{site.baseurl}}/assets/img/MegengineDenoisingCompetition/pic1.png)

我是用的baseline我会放在我的Github上，如果有需要可以从[https://github.com/setwgx/MegengineDenoisingCompetition](https://github.com/setwgx/MegengineDenoisingCompetition)下载。由于Github不能上传超过25M的文件，所以数据集等没有上传，只添加相同名字的文件用于展示文件结构。
