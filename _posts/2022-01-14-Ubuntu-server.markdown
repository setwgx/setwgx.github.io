---
layout: post
title: Ubuntu Server Configuration
date: 2022-01-14 21:07:31 +0300
description: Record the installation process of the entire ubuntu server. # Add post description (optional)
img: /Ubuntu-linux-server/cover.png # Add image post (optional)
tags: [Ubuntu, Server, Anaconda3, NVIDIA-DRIVER, CUDA, cuDNN] # add tag
---

## Preface

I recently purchased a new server, and it is necessary to configure the server to facilitate our experiments. This article records the entire process of installing the ubuntu server version from a blank server. For your reference only.

Before we start the tutorial, we need to explain a few things. Since we are buying a Supermicro X11 motherboard, there are a few things that need to be explained in advance. There are two things to note:</font color="red">1.When installing the ubuntu server version, remember to change the domestic source at the download source (it is recommended to use Tsinghua source), and use a network cable that does not require authentication, otherwise the installation will report an error! ! !</font></font color="red">2.The server uses the F11 key to enter the boot disk or the BIOS configuration interface.</font>

(This document is only for the installation of the ubuntu-server version)

## Anaconda

##### installation
In the non-GUI interface we use the command line to install Anaconda3.

Since its server is abroad, it will be slower to download the program using the original Anaconda3 website. Use the domestic source to download, first find the mirror and select the required version.

Tsinghua source：[https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)

Select the 64-bit version released in 2021.11

Anaconda3-2021.11-Linux-x86_64.sh

![photo1]({{site.baseurl}}/assets/img/Ubuntu-linux-server/1.png){:width="70%"}

sha256sum verification is performed on the downloaded file.(skipable)

![photo2]({{site.baseurl}}/assets/img/Ubuntu-linux-server/2.png){:width="70%"}

The corresponding hash can be found in the Anaconda [documentation](https://docs.anaconda.com/anaconda/install/hashes/lin-3-64/).

![photo3]({{site.baseurl}}/assets/img/Ubuntu-linux-server/3.png){:width="70%"}

As shown in sha256 verification，fedf9e340039557f7b5e8a8a86affa9d299f5e9820144bd7b92ae9f7ee08ac60

Start the installer, bash is followed by the downloaded file script, and the following interface will appear.

![photo4]({{site.baseurl}}/assets/img/Ubuntu-linux-server/4.png){:width="70%"}

Press ENTER to read the agreement, after reading it will prompt whether to accept the agreement.

![photo5]({{site.baseurl}}/assets/img/Ubuntu-linux-server/5.png){:width="70%"}

Enter **yes** to agree to the terms of the agreement

```shell
Anaconda3 will now be installed into this location:
/home/linuxize/anaconda3

    - Press ENTER to confirm the location
    - Press CTRL-C to abort the installation
- Or specify a different location below
```

After agreeing, an installation prompt will appear. You can choose to directly enter ENTER to install the default directory, or you can specify your own installation path. Generally, the default installation is enough. Wait for the prompt after the installation is completed.

```shell
Installation finished.
Do you wish the installer to initialize Anaconda3
by running conda init? [yes|no]
```
After agreeing, the program will add the command-line tool conda to the system's PATH environment variable.

To activate Anaconda, you can close and reopen your shell or reload the PATH environment variable by entering the following command in the current shell session:

```shell
source ~/.bashrc
```

Anaconda3 is installed.

##### update
```shell
conda update –all
```

You can update your Anaconda3 with instructions later.

##### uninstallation
If you want to uninstall Anaconda from your Ubuntu system, remove the Anaconda installation directory and other files created during installation:

```shell
rm -rf ~/anaconda3 ~/.condarc ~/.conda ~/.continuum
```

After deleting the folder, you can continue to clean up the Anaconda path in .bashrc:

```shell
sudo gedit ~/.bashrc
```

At the end of the .bashrc file, use the # sign to comment out the path added before (or delete it directly):

```shell
#export PATH=/home/lq/anaconda3/bin:$PATH
```

save and close the file.

To make it take effect immediately, execute in the terminal:

```shell
source ~/.bashrc
```

Close the terminal, and then restart a new terminal, this step is very important, otherwise anaconda3 will still be bound to the original terminal.


## NVIDIA Driver

| Software and hardware information |
|:-------------------:|
| Operating System：Ubuntu20.04 |
| Graphics Processor：GeForce RTX 3090 |

According to the graphics card model 3090, go to the [official website](https://www.nvidia.com/Download/index.aspx) to download the corresponding driver. The configuration selected here is as follows, and the version 470.94 is selected.

![photo6]({{site.baseurl}}/assets/img/Ubuntu-linux-server/6.png){:width="70%"}

Find the download link of the corresponding file through the download button.

Since there is no GUI for connecting to the remote server, here is to find it on other computers. After obtaining the URL, use wget to download the file in the remote server.

![photo7]({{site.baseurl}}/assets/img/Ubuntu-linux-server/7.png){:width="70%"}

The link obtained here is as follows [this](https://us.download.nvidia.com/XFree86/Linux-x86_64/470.94/NVIDIA-Linux-x86_64-470.94.run), you can download it using wget

![photo8]({{site.baseurl}}/assets/img/Ubuntu-linux-server/8.png){:width="70%"}

(Due to the direct configuration on the server side, there is no screenshot)

1 Remove the NVIDIA drivers

```shell
sudo apt-get remove –purge nvidia*
```

2 Enter the computer's BIOS and set Security Boot to Disabled

If secure boot is not disabled, it may cause the Nvidia driver to install incorrectly.

3 Enter the computer's BIOS and set Security Boot to Disabled

In fact, ubuntu uses the open source nouveau driver as the driver for Nvidia graphics processor, which needs to be disabled.

Open the configuration file for editing:

```shell
sudo vim /etc/modprobe.d/blacklist.conf
```

4 Add on the last line:

```shell
blacklist nouveau
```

This one is used to disable the nouveau driver, and you don't need to change it back later.

5 Since nouveau is built in the kernel, execute the following command to take effect:

```shell
sudo update-initramfs -u
```

6 Reboot

```shell
Reboot
```

7 After restarting, you can see if nouveau is running:

```shell
lsmod | grep nouveau  
```

No output indicates that the disable is in effect.

8 Add executable permissions to the driver file:

```shell
sudo chmod a+x NVIDIA-Linux-x86_64-460.32.03.run
```

9 Before running NVIDIA-Driver, you must install compilation environments such as gcc, g++ and make

```shell
Sudo apt install gcc
Sudo apt install g++
Sudo apt install make
```

10 Installation

```shell
sudo sh ./NVIDIA-Linux-x86_64-460.32.03.run –no-x-check –no-nouveau-check –no-opengl-files
```

Then follow the installation instructions to complete.

After the installation is complete, restart and use nvidia-smi to verify whether the installation is successful.

![photo9]({{site.baseurl}}/assets/img/Ubuntu-linux-server/9.png){:width="70%"}

## CUDA installation

##### installation
<font color="red">(If you installed NVIDIA-DRIVER first, you can pay attention to whether CUDA is installed. If you enter the command stat cuda under /usr/local to show that CUDA is installed, you can skip the CUDA installation or install multiple versions of CUDA according to the following tutorial. )</font>

Before downloading CUDA, you need to match the computing power of your graphics processor. The following figure is the corresponding table of the corresponding computing power graphics processor version and CUDA version. Please check the latest version on the [official website](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html).

![photo10]({{site.baseurl}}/assets/img/Ubuntu-linux-server/10.png){:width="70%"}

In order to correspond to the previously configured server, the CUDA version 11.4.0 is selected here.

Check the CUDA download link on its [official website](https://developer.nvidia.com/cuda-downloads and choose according to your server configuration) and choose according to your server configuration.

Here, the installation file of the runfile type is selected so that the installation path of each cuda can be set later.

![photo11]({{site.baseurl}}/assets/img/Ubuntu-linux-server/11.png){:width="70%"}

The obtained installation instructions are as follows, enter the corresponding instructions to download CUDA.

```shell
wget https://developer.download.nvidia.com/compute/cuda/11.4.0/local_installers/cuda_11.4.0_470.42.01_linux.run
```

![photo12]({{site.baseurl}}/assets/img/Ubuntu-linux-server/12.png){:width="70%"}

Install the relevant dependencies, otherwise an error may be reported, otherwise the `Missing recommended library` error will appear.

```shell
sudo apt-get install freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libgl1-mesa-glx libglu1-mesa libglu1-mesa-dev
```

start running the installation cuda program.

```shell
sudo sh cuda_11.4.0_470.42.01_linux.run
```

![photo13]({{site.baseurl}}/assets/img/Ubuntu-linux-server/13.png){:width="70%"}

Accept the above EULA: accept.

![photo14]({{site.baseurl}}/assets/img/Ubuntu-linux-server/14.png){:width="70%"}

Since the NVIDIA driver has been installed, cancel the driver installation and check the installation.

![photo15]({{site.baseurl}}/assets/img/Ubuntu-linux-server/15.png){:width="70%"}

Since the 10.1 version of CUDA has been installed before, if you need to use a new CUDA version, select Yes, which will point the CUDA soft link to the latest 11.4 version, otherwise just install a CUDA version, and you can reset the soft link yourself later. . After waiting for the installation, it will take a certain amount of time.

![photo16]({{site.baseurl}}/assets/img/Ubuntu-linux-server/16.png){:width="70%"}

```shell
===========
= Summary =
===========

Driver:   Not Selected
Toolkit:  Installed in /usr/local/cuda-11.4/
Samples:  Installed in /home/rtx-3090/
```

The installation is complete when a message like the above appears, but remember to see if there are other warnings in the message.

you need to configure CUDA-related environment variables in the end.

```shell
vim ~/.bashrc
```

![photo17]({{site.baseurl}}/assets/img/Ubuntu-linux-server/17.png){:width="70%"}

#Add at the end of the file.

```shell
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64
export PATH=$PATH:/usr/local/cuda/bin
export CUDA_HOME=$CUDA_HOME:/usr/local/cuda
```

Note: During the installation process, pay attention to the choice of cuda symbol link (for the first installation, choose y, for additional versions, choose n)

##### CUDA switch version
You can use the stat command to see which cuda version the current cuda soft link points to, as follows:

![photo18]({{site.baseurl}}/assets/img/Ubuntu-linux-server/18.png){:width="70%"}

It can be seen that the cuda file type is symbolic link, and the directory pointed to is /usr/local/cuda-11.4. When we want to use the cuda-10.1 version, we only need to delete the soft link, and then re-establish the point to cuda-10.1 The soft link of the version is enough (note that the name is still cuda, because it should be consistent with the setting in the bashrc file).

```shell
sudo rm -rf cuda
sudo ln -s /usr/local/cuda-10.1 /usr/local/cuda
```

If you want to switch to other versions of cuda, you only need to change the correct path of cuda when creating a soft link.

## cuDNN installation
Download cuDNN corresponding to CUDA. Here choose to download version 8.2.4 for CUDA11.4 (requires login and registration before downloading).

![photo19]({{site.baseurl}}/assets/img/Ubuntu-linux-server/19.png){:width="70%"}

Since registration is required, use WinSCP to transfer files to the server after downloading directly on other computers.

![photo20]({{site.baseurl}}/assets/img/Ubuntu-linux-server/20.png){:width="70%"}

If the archive is not in .tgz format, rename the archive to .tgz format.

```shell
sudo tar -zvxf cudnn-11.4*.tar.gz
```

Unzip the current package in .tgz format, the unzipped folder is named cuda, and the folder contains two folders: one is include and the other is lib64.

![photo21]({{site.baseurl}}/assets/img/Ubuntu-linux-server/21.png){:width="70%"}

Copy the relevant files to the CUDA path while raising the permissions of the files.

Then re-establish the cuda soft link under /usr/local.

```shell
cd /usr/local
sudo rm -rf cuda
sudo ln -s cuda-11.1 cuda
```

The new version of the method to view cuDNN.

```shell
cat /usr/local/cuda/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
```

![photo22]({{site.baseurl}}/assets/img/Ubuntu-linux-server/22.png){:width="70%"}

If the above information is displayed, the installation is successful.

![photo23]({{site.baseurl}}/assets/img/Ubuntu-linux-server/23.png){:width="70%"}

It can also be tested in a deep learning environment using torch.cuda.is_available()

## other problems
**Problem: The server ssh is automatically interrupted after a period of time and cannot be connected again. The server must be restarted. The reason is that the server is set to suspend automatically.**

（Note that this is only applicable to ubuntu server, no graphical interface is required, and no special consideration and verification of the graphical interface is carried out! Readers who rely on the graphical operation interface should use it with caution!）

input the command:
```shell
systemctl status sleep.target
```

![photo24]({{site.baseurl}}/assets/img/Ubuntu-linux-server/24.png){:width="70%"}

Loaded can be found. Indicates that automatic sleep is enabled.

input the command:
```shell
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```
disable it.

input the command:
```shell
systemctl status sleep.target
```

![photo25]({{site.baseurl}}/assets/img/Ubuntu-linux-server/25.png){:width="70%"}

Indicates that the disable was successful.

<br>

**Problem: Inconsistency between nvcc and stat cuda**

When we use nvcc –version and stat cuda to query separately, the cuda versions displayed by the two are inconsistent, and the cuda versions of nvcc are consistent and unchanged.

The reason is that nvcc points to the default nvidia-cuda-toolkit location of the system, as shown in the following figure.

![photo26]({{site.baseurl}}/assets/img/Ubuntu-linux-server/26.png){:width="70%"}

So when we use nvcc –version to view the version, it is inconsistent with the version displayed by stat cuda, but it has actually been switched successfully.

So we need to change the default cuda-toolkit pointed by nvcc to point to our cuda-toolkit installation location.

```shell
sudo vim /usr/bin/nvcc
exec /usr/local/cuda/bin/nvcc
```

![photo27]({{site.baseurl}}/assets/img/Ubuntu-linux-server/27.png){:width="70%"}

![photo28]({{site.baseurl}}/assets/img/Ubuntu-linux-server/28.png){:width="70%"}

After the modification is completed, the content displayed by the two can be successfully made consistent, and the inconsistency may lead to some unexpected errors.

## static ip configuration
In Ubuntu18.04, the traditional configuration /etc/network/interfaces is useless, the new network configuration file is in: /etc/netplan/50-cloud-init.yaml

```shell
sudo vim /etc/netplan/50-cloud-init.yaml
```

![photo29]({{site.baseurl}}/assets/img/Ubuntu-linux-server/29.png){:width="70%"}

Use vim to edit the file, pay attention to the format, there is a space after the colon.

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

eno2：your current network card name;

dhcp4：The dhcp switch of the ipv4 interface, false is turned off, true is turned on;

addresses：static IP address/mask;

gateway4：ipv4 gateway;

nameservers：DNS server and comma separated;

After editing, save the configuration file and execute the command to restart the network service to take effect.

```shell
sudo netplan apply
```

![photo30]({{site.baseurl}}/assets/img/Ubuntu-linux-server/30.png){:width="70%"}

## References

Anaconda3 installation [https://cloud.tencent.com/developer/article/1649008](https://cloud.tencent.com/developer/article/1649008)

Anaconda3 uninstall [https://blog.csdn.net/Rocky6688/article/details/103032620/](https://blog.csdn.net/Rocky6688/article/details/103032620/)

NVIDIA driver [https://blog.csdn.net/TracelessLe/article/details/112687973](https://blog.csdn.net/TracelessLe/article/details/112687973)

CUDA and NVIDIA driver version correspondence table [https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html)

CUDA official website [https://developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads)

Inconsistency between nvcc –version and stat cuda version [https://www.cnblogs.com/hf19950918/p/14707662.html](https://www.cnblogs.com/hf19950918/p/14707662.html)

NVIDIA cuDNN official website [https://developer.nvidia.com/rdp/cudnn-archive](https://developer.nvidia.com/rdp/cudnn-archive)

cuDNN query method [https://www.pianshen.com/article/72642069842/](https://www.pianshen.com/article/72642069842/)

Solve the problem that the server hangs automatically [https://blog.csdn.net/happywlg123/article/details/121244526](https://blog.csdn.net/happywlg123/article/details/121244526)

static ip configuration [https://www.cnblogs.com/hkleak/p/12904986.html](https://www.cnblogs.com/hkleak/p/12904986.html)

<br>

**<font color="red" >Disclaimer: The main purpose of this document is to document the configuration process for future reference. The above steps are derived from various attempts, and can only be shown to be applicable to the software and hardware environment I use, and may not be universal. Please judge by yourself whether to refer to and use it, and the resulting risks are at your own risk.</font>**