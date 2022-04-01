---
layout: post
title: 全新Ubuntu服务器配置
date: 2022-01-14 21:07:31 +0300
description: 记录整个ubuntu服务器的安装过程。 # Add post description (optional)
img: /Ubuntu-linux-server/cover.png # Add image post (optional)
tags: [Ubuntu, Server, Anaconda3, NVIDIA-DRIVER, CUDA, cuDNN] # add tag
---

## 序

最近购买了一台新的服务器，为了方便我们的实验，需要对服务器进行配置。 本文记录了从空白服务器安装ubuntu服务器版本的全过程。 仅供您参考。

在开始本教程之前，我们需要解释一些事情。 由于我们购买的是 Supermicro X11 主板，并且所处环境比较特殊，所以有几件事需要提前说明。 有两点需要注意：<font color="red">1.安装ubuntu服务器版时，记得在下载源处更改国内源（建议使用清华源），并使用不需要认证的网线，否则安装会报错！</font><font color="red">2.服务器使用F11键进入启动盘或BIOS配置界面。</font>

（本文档只针对ubuntu-server版本的安装）

## Anaconda

##### 安装
在非GUI界面我们使用命令行安装Anaconda3。

由于其服务器在国外，使用Anaconda3原网站下载程序会比较慢。 使用国内源下载，先找到镜像，选择需要的版本。

清华源：[https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)

选择2021.11发布的64位版本。

Anaconda3-2021.11-Linux-x86_64.sh

![photo1]({{site.baseurl}}/assets/img/Ubuntu-linux-server/1.png){:width="70%"}

对下载的文件进行sha256sum验证。（可跳过）

![photo2]({{site.baseurl}}/assets/img/Ubuntu-linux-server/2.png){:width="70%"}

对应的hash可以在Anaconda [官方文档](https://docs.anaconda.com/anaconda/install/hashes/lin-3-64/)中找到.

![photo3]({{site.baseurl}}/assets/img/Ubuntu-linux-server/3.png){:width="70%"}

如图sha256验证，fedf9e340039557f7b5e8a8a86affa9d299f5e9820144bd7b92ae9f7ee08ac60

启动安装程序，bash后面跟着下载的文件脚本，会出现如下界面。

![photo4]({{site.baseurl}}/assets/img/Ubuntu-linux-server/4.png){:width="70%"}

按ENTER键阅读协议，阅读后提示是否接受协议。

![photo5]({{site.baseurl}}/assets/img/Ubuntu-linux-server/5.png){:width="70%"}

输入**yes**同意协议条款

```shell
Anaconda3 will now be installed into this location:
/home/linuxize/anaconda3

    - Press ENTER to confirm the location
    - Press CTRL-C to abort the installation
- Or specify a different location below
```

同意后会出现安装提示。 可以选择直接回车安装默认目录，也可以自己指定安装路径。 一般默认安装就够了。 安装完成后等待提示。

```shell
Installation finished.
Do you wish the installer to initialize Anaconda3
by running conda init? [yes|no]
```
同意后，程序会将命令行工具conda添加到系统的PATH环境变量中。

要激活 Anaconda，您可以通过在当前 shell 会话中输入以下命令来关闭并重新打开 shell 或重新加载 PATH 环境变量：

```shell
source ~/.bashrc
```

Anaconda3 安装完毕。

##### 更新
```shell
conda update –all
```

你可以在未来使用说明更新您的 Anaconda3。

##### 卸载
如果要从 Ubuntu 系统中卸载 Anaconda，请删除 Anaconda 安装目录和安装过程中创建的其他文件：

```shell
rm -rf ~/anaconda3 ~/.condarc ~/.conda ~/.continuum
```

删除文件夹后，可以继续清理 .bashrc 中的Anaconda路径：

```shell
sudo gedit ~/.bashrc
```

在.bashrc文件的最后，用#号注释掉之前添加的路径（或者直接删除）：

```shell
#export PATH=/home/lq/anaconda3/bin:$PATH
```

保存并关闭文件。

要使其立即生效，请在终端中执行：

```shell
source ~/.bashrc
```

关闭终端，然后重启一个新的终端，这一步很重要，否则 anaconda3 还是会绑定到原来的终端上。

<br />
## NVIDIA驱动

| Software and hardware information |
|:-------------------:|
| Operating System：Ubuntu20.04 |
| Graphics Processor：GeForce RTX 3090 |

根据显卡型号3090, 到 [NVIDIA官方网站](https://www.nvidia.com/Download/index.aspx) 下载相应的驱动程序。 这里选择的配置如下，选择的是470.94版本。

![photo6]({{site.baseurl}}/assets/img/Ubuntu-linux-server/6.png){:width="70%"}

通过下载按钮找到对应文件的下载链接。

由于没有用于连接远程服务器的 GUI，这里是在其他计算机上找到它。 获取 URL 后，使用 wget 下载远程服务器中的文件。

![photo7]({{site.baseurl}}/assets/img/Ubuntu-linux-server/7.png){:width="70%"}

这里得到的 [链接](https://us.download.nvidia.com/XFree86/Linux-x86_64/470.94/NVIDIA-Linux-x86_64-470.94.run) 如下, 你可以使用 wget 下载它 。

![photo8]({{site.baseurl}}/assets/img/Ubuntu-linux-server/8.png){:width="70%"}

（由于服务器端直接配置，所以没有截图）

1 卸载NVIDIA驱动

```shell
sudo apt-get remove –purge nvidia*
```

2 进入计算机的 BIOS 并将 Security Boot 设置为 Disabled

如果没有禁用安全启动，可能会导致 Nvidia 驱动程序安装不正确。

3 进入计算机的 BIOS 并将 Security Boot 设置为 Disabled

事实上，ubuntu 使用的是开源的 nouveau 驱动作为 NVIDIA 图形处理器的驱动，需要禁用。

打开配置文件进行编辑：

```shell
sudo vim /etc/modprobe.d/blacklist.conf
```

4 在最后一行添加：

```shell
blacklist nouveau
```

这个是用来禁用 nouveau 驱动的，以后不用改回来。

5 由于nouveau是内置在内核中的，所以执行如下命令生效：

```shell
sudo update-initramfs -u
```

6 重启

```shell
Reboot
```

7 重启后可以看到nouveau是否在运行：

```shell
lsmod | grep nouveau  
```

无输出表示禁用已生效。

8 给驱动文件添加可执行权限：

```shell
sudo chmod a+x NVIDIA-Linux-x86_64-460.32.03.run
```

9 运行 NVIDIA-Driver 前，必须安装 gcc、g++、make 等编译环境

```shell
Sudo apt install gcc
Sudo apt install g++
Sudo apt install make
```

10 安装

```shell
sudo sh ./NVIDIA-Linux-x86_64-460.32.03.run –no-x-check –no-nouveau-check –no-opengl-files
```

然后按照安装说明完成。

安装完成后重启，使用nvidia-smi验证是否安装成功。

![photo9]({{site.baseurl}}/assets/img/Ubuntu-linux-server/9.png){:width="70%"}

<br />
## 安装CUDA

##### 安装
<font color="red">(如果先安装了NVIDIA-DRIVER，可以注意是否安装了CUDA。 如果在 /usr/local 下输入命令 stat cuda 显示 CUDA 已安装，则可以跳过安装 CUDA 或按照以下教程安装多个版本的 CUDA。)</font>

在下载 CUDA 之前，您需要匹配您的图形处理器的计算能力。 下图是对应算力图形处理器版本和CUDA版本的对应表。 请在[官方网站](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html)查看最新版本。

![photo10]({{site.baseurl}}/assets/img/Ubuntu-linux-server/10.png){:width="70%"}

为了对应之前配置的服务器，这里选择CUDA 11.4.0版本。

检查该版本 CUDA 在[官网](https://developer.nvidia.com/cuda-downloads)的下载链接，并且并根据您的服务器配置进行选择。

这里选择runfile类型的安装文件，以便以后设置各个cuda的安装路径。

![photo11]({{site.baseurl}}/assets/img/Ubuntu-linux-server/11.png){:width="70%"}

得到的安装指令如下，输入相应的 CUDA 安装指令。

```shell
wget https://developer.download.nvidia.com/compute/cuda/11.4.0/local_installers/cuda_11.4.0_470.42.01_linux.run
```

![photo12]({{site.baseurl}}/assets/img/Ubuntu-linux-server/12.png){:width="70%"}

安装相关依赖，否则可能会报错，否则会出现 `缺少相关库` 的错误。

```shell
sudo apt-get install freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libgl1-mesa-glx libglu1-mesa libglu1-mesa-dev
```

开始运行安装 cuda 程序。

```shell
sudo sh cuda_11.4.0_470.42.01_linux.run
```

![photo13]({{site.baseurl}}/assets/img/Ubuntu-linux-server/13.png){:width="70%"}

接受上述协议： accept。

![photo14]({{site.baseurl}}/assets/img/Ubuntu-linux-server/14.png){:width="70%"}

由于已经安装了NVIDIA驱动，取消驱动安装，检查安装。

![photo15]({{site.baseurl}}/assets/img/Ubuntu-linux-server/15.png){:width="70%"}

由于之前已经安装了10.1版本的CUDA，如果需要使用新的CUDA版本，选择Yes，会将CUDA软链接指向最新的11.4版本，否则只会安装一个CUDA版本，可以重置软链接，稍后链接自己需要的CUDA版本，等待安装，需要一定的时间。

![photo16]({{site.baseurl}}/assets/img/Ubuntu-linux-server/16.png){:width="70%"}

```shell
===========
= Summary =
===========

Driver:   Not Selected
Toolkit:  Installed in /usr/local/cuda-11.4/
Samples:  Installed in /home/rtx-3090/
```

出现如上的提示信息即表示安装完成，但记得查看提示信息中是否还有其他警告。

最后需要配置CUDA相关的环境变量。

```shell
vim ~/.bashrc
```

![photo17]({{site.baseurl}}/assets/img/Ubuntu-linux-server/17.png){:width="70%"}

#在文件末尾添加如下条目。

```shell
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64
export PATH=$PATH:/usr/local/cuda/bin
export CUDA_HOME=$CUDA_HOME:/usr/local/cuda
```

注意：安装过程中注意cuda符号链接的选择（第一次安装选择y，其他版本选择n）

##### CUDA版本切换
可以使用stat命令查看当前cuda软链接指向哪个cuda版本，如下：

![photo18]({{site.baseurl}}/assets/img/Ubuntu-linux-server/18.png){:width="70%"}

可以看出cuda文件类型是符号链接，指向的目录是/usr/local/cuda-11.4。 当我们要使用cuda-10.1版本的时候，只需要删除软链接，然后重新建立指向cuda-10.1版本的软链接就足够了（注意名字还是cuda，因为它 应与 bashrc 文件中的设置一致）。

```shell
sudo rm -rf cuda
sudo ln -s /usr/local/cuda-10.1 /usr/local/cuda
```

如果要切换到其他版本的cuda，只需要在创建软链接时更改cuda的正确路径即可。

<br />
## 安装cuDNN
下载CUDA对应的cuDNN。 这里选择下载CUDA11.4的8.2.4版本（下载前需要登录注册）。

![photo19]({{site.baseurl}}/assets/img/Ubuntu-linux-server/19.png){:width="70%"}

由于需要注册，所以直接在其他电脑上下载后使用WinSCP将文件传输到服务器。

![photo20]({{site.baseurl}}/assets/img/Ubuntu-linux-server/20.png){:width="70%"}

如果存档不是 .tgz 格式，请将存档重命名为 .tgz 格式。

```shell
sudo tar -zvxf cudnn-11.4*.tar.gz
```

将当前包解压为.tgz格式，解压后的文件夹名为cuda，文件夹包含两个文件夹：一个是include，一个是lib64。

![photo21]({{site.baseurl}}/assets/img/Ubuntu-linux-server/21.png){:width="70%"}

将相关文件复制到CUDA路径，同时提升文件权限。

然后在/usr/local下重新建立cuda软链接。

```shell
cd /usr/local
sudo rm -rf cuda
sudo ln -s cuda-11.1 cuda
```

新版本查看cuDNN的方法。

```shell
cat /usr/local/cuda/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
```

![photo22]({{site.baseurl}}/assets/img/Ubuntu-linux-server/22.png){:width="70%"}

如果显示以上信息，则安装成功。

![photo23]({{site.baseurl}}/assets/img/Ubuntu-linux-server/23.png){:width="70%"}

也可以使用 torch.cuda.is_available() 在深度学习环境中进行测试

<br />
## 其他问题
**问题：服务器ssh一段时间后自动中断，无法再次连接。 必须重新启动服务器。 原因是服务器设置为自动挂起。**

（注意这里只适用于ubuntu服务器，不需要图形界面，也不需要对图形界面进行特殊考虑和验证！ 依赖图形操作界面的读者慎用！）

输入命令：
```shell
systemctl status sleep.target
```

![photo24]({{site.baseurl}}/assets/img/Ubuntu-linux-server/24.png){:width="70%"}

加载后可以找到。 表示启用了自动休眠。

输入命令：
```shell
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```
禁用它。

输入命令：
```shell
systemctl status sleep.target
```

![photo25]({{site.baseurl}}/assets/img/Ubuntu-linux-server/25.png){:width="70%"}

表示禁用成功。

<br>

**问题：nvcc 和 stat cuda 不一致。**

当我们使用nvcc –version和stat cuda分别查询时，两者显示的cuda版本不一致，nvcc的cuda版本一致且不变。

原因是nvcc指向系统默认的nvidia-cuda-toolkit位置，如下图所示。

![photo26]({{site.baseurl}}/assets/img/Ubuntu-linux-server/26.png){:width="70%"}

所以当我们使用nvcc –version查看版本时，与stat cuda显示的版本不一致，但实际上已经切换成功。

所以我们需要将nvcc指向的默认cuda-toolkit改成指向我们的cuda-toolkit安装位置。

```shell
sudo vim /usr/bin/nvcc
exec /usr/local/cuda/bin/nvcc
```

![photo27]({{site.baseurl}}/assets/img/Ubuntu-linux-server/27.png){:width="70%"}

![photo28]({{site.baseurl}}/assets/img/Ubuntu-linux-server/28.png){:width="70%"}

修改完成后，可以成功使两者显示的内容一致，不一致可能会导致一些意想不到的错误。

<br />
## 静态ip配置
在Ubuntu18.04中，传统配置/etc/network/interfaces没用，新的网络配置文件在：/etc/netplan/50-cloud-init.yaml

```shell
sudo vim /etc/netplan/50-cloud-init.yaml
```

![photo29]({{site.baseurl}}/assets/img/Ubuntu-linux-server/29.png){:width="70%"}

使用vim编辑文件，注意格式，冒号后面有个空格。

```shell
network:

    ethernets:

        eno2:

            dhcp4: false

            addresses: [192.168.37.188/24]

            gateway4: 192.168.37.2

            nameservers:

                addresses: [114.114.114.114,8.8.8.8]

    version: 2
```

eno2：你当前的网卡名称；

dhcp4：ipv4接口的dhcp开关，false为关闭，true为开启；

地址：静态IP地址/掩码；

gateway4：ipv4网关；

nameservers：DNS服务器，逗号分隔；

编辑好后保存配置文件，执行命令重启网络服务生效。

```shell
sudo netplan apply
```

![photo30]({{site.baseurl}}/assets/img/Ubuntu-linux-server/30.png){:width="70%"}

<br />
## 参考资料

Anaconda3 安装 [https://cloud.tencent.com/developer/article/1649008](https://cloud.tencent.com/developer/article/1649008)

Anaconda3 卸载 [https://blog.csdn.net/Rocky6688/article/details/103032620/](https://blog.csdn.net/Rocky6688/article/details/103032620/)

英伟达驱动 [https://blog.csdn.net/TracelessLe/article/details/112687973](https://blog.csdn.net/TracelessLe/article/details/112687973)

CUDA和NVIDIA驱动版本对应表 [https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html)

CUDA官方网站 [https://developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads)

Inconsistency between nvcc –version and stat cuda version [https://www.cnblogs.com/hf19950918/p/14707662.html](https://www.cnblogs.com/hf19950918/p/14707662.html)

nvcc –version 和 stat cuda 版本不一致 [https://developer.nvidia.com/rdp/cudnn-archive](https://developer.nvidia.com/rdp/cudnn-archive)

cuDNN查询方法  [https://www.pianshen.com/article/72642069842/](https://www.pianshen.com/article/72642069842/)

解决服务器自动挂起的问题 [https://blog.csdn.net/happywlg123/article/details/121244526](https://blog.csdn.net/happywlg123/article/details/121244526)

静态ip配置  [https://www.cnblogs.com/hkleak/p/12904986.html](https://www.cnblogs.com/hkleak/p/12904986.html)

<br>

**<font color="red" >免责声明：本文档的主要目的是记录配置过程以供将来参考。 以上步骤来源于各种尝试，只能证明适用于我使用的软硬件环境，不一定通用。 请自行判断是否参考和使用，由此产生的风险自负。</font>**