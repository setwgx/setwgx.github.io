---
layout: post
title: DOCKER DESKTOP
date: 2023-03-29 23:18:43 +0300
description: Docker的安装使用记录 # Add post description (optional)
img: /docker/cover.png # Add image post (optional)
tags: [Linux， Docker， Windows] # add tag
---

# 前言 
## 环境配置的难题
软件开发最大的麻烦事之一，就是环境配置。用户计算机的环境都不相同，你怎么知道自家的软件，能在那些机器跑起来？

用户必须保证两件事：操作系统的设置，各种库和组件的安装。只有它们都正确，软件才能运行。举例来说，安装一个 Python 应用，计算机必须有 Python 引擎，还必须有各种依赖，可能还要配置环境变量。

如果某些老旧的模块与当前环境不兼容，那就麻烦了。开发者常常会说："它在我的机器可以跑了"（It works on my machine），言下之意就是，其他机器很可能跑不了。

环境配置如此麻烦，换一台机器，就要重来一次，旷日费时。很多人想到，能不能从根本上解决问题，软件可以带环境安装？也就是说，安装的时候，把原始环境一模一样地复制过来。

## 虚拟机
虚拟机（virtual machine）就是带环境安装的一种解决方案。它可以在一种操作系统里面运行另一种操作系统，比如在 Windows 系统里面运行 Linux 系统。应用程序对此毫无感知，因为虚拟机看上去跟真实系统一模一样，而对于底层系统来说，虚拟机就是一个普通文件，不需要了就删掉，对其他部分毫无影响。

虽然用户可以通过虚拟机还原软件的原始环境。但是，这个方案有几个缺点。

（1）资源占用多

虚拟机会独占一部分内存和硬盘空间。它运行的时候，其他程序就不能使用这些资源了。哪怕虚拟机里面的应用程序，真正使用的内存只有 1MB，虚拟机依然需要几百 MB 的内存才能运行。

（2）冗余步骤多

虚拟机是完整的操作系统，一些系统级别的操作步骤，往往无法跳过，比如用户登录。

（3）启动慢

启动操作系统需要多久，启动虚拟机就需要多久。可能要等几分钟，应用程序才能真正运行。

## Linux容器
由于虚拟机存在这些缺点，Linux 发展出了另一种虚拟化技术：Linux 容器（Linux Containers，缩写为 LXC）。

Linux 容器不是模拟一个完整的操作系统，而是对进程进行隔离。或者说，在正常进程的外面套了一个保护层。对于容器里面的进程来说，它接触到的各种资源都是虚拟的，从而实现与底层系统的隔离。

由于容器是进程级别的，相比虚拟机有很多优势。

（1）启动快

容器里面的应用，直接就是底层系统的一个进程，而不是虚拟机内部的进程。所以，启动容器相当于启动本机的一个进程，而不是启动一个操作系统，速度就快很多。

（2）资源占用少

容器只占用需要的资源，不占用那些没有用到的资源；虚拟机由于是完整的操作系统，不可避免要占用所有资源。另外，多个容器可以共享资源，虚拟机都是独享资源。

（3）体积小

容器只要包含用到的组件即可，而虚拟机是整个操作系统的打包，所以容器文件比虚拟机文件要小很多。

总之，容器有点像轻量级的虚拟机，能够提供虚拟化的环境，但是成本开销小得多。

## Docker是什么
Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口。它是目前最流行的 Linux 容器解决方案。

Docker 将应用程序与该程序的依赖，打包在一个文件里面。运行这个文件，就会生成一个虚拟容器。程序在这个虚拟容器里运行，就好像在真实的物理机上运行一样。有了 Docker，就不用担心环境问题。

总体来说，Docker 的接口相当简单，用户可以方便地创建和使用容器，把自己的应用放入容器。容器还可以进行版本管理、复制、分享、修改，就像管理普通的代码一样。

Docker 就像一个盒子，里面可以装很多物件，如果需要某些物件，可以直接将该盒子拿走，而不需要从该盒子中一件一件的取。

Docker[中文社区](https://www.docker.org.cn/index.html)、Docker[中文文档](https://www.kancloud.cn/jingyucloud/docker/216412)

## Docker的用途
Docker 的主要用途，目前有三大类。

（1）提供一次性的环境。比如，本地测试他人的软件、持续集成的时候提供单元测试和构建的环境。

（2）提供弹性的云服务。因为 Docker 容器可以随开随关，很适合动态扩容和缩容。

（3）组建微服务架构。通过多个容器，一台机器可以跑多个服务，因此在本机就可以模拟出微服务架构。

## Docker的基本概念
### 镜像（Image）
Docker的镜像概念类似于虚拟机里的镜像(比如.ISO文件)，是一个只读的模板，一个独立的文件系统，包括运行容器所需的数据，可以用来创建新的容器。

例如：一个镜像可以包含一个完整的 ubuntu 操作系统环境，里面仅安装了MySQL或用户需要的其它应用程序。

这里的镜像就如下方虚拟机创建时候使用的镜像类似。这个镜像便于移动，并且这个镜像我们可以交给任何人使用，其他人使用的时候也很方便，只需要将其实例化即可。

![镜像]({{site.baseurl}}/assets/img/docker/镜像.png)

### 容器（Container）
Docker容器是由Docker镜像创建的运行实例，类似VM虚拟机，支持启动，停止，删除等。

每个容器间是相互隔离的，容器中会运行特定的应用，包含特定应用的代码及所需的依赖文件。

容器就类似与虚拟机中我们创建好的虚拟机系统，之后我们所有的操作都是在容器中进行的，我们的程序也是运行在容器中。

![容器]({{site.baseurl}}/assets/img/docker/容器.png)

### 仓库（Repository）
镜像便于传播，而仓库就是专门用来传播这些镜像的地方，他有点类似与Github，或者你可以把他看成一个存放各种镜像的镜像商店。

Docker[官方的仓库](https://hub.docker.com/): 他的服务器处于国外，所以下载速度较慢，不过我们可以通过换源解决。

daocloud[国内仓库](https://hub.daocloud.io/): 国内也有一些优秀的商店，他和Docker官方的仓库的区别类似与Github和Gitee的区别。

[网易云镜像中心](https://c.163yun.com/hub#/home)

## 本文目标
本问主要会介绍在Windows系统下Docker Desktop的安装，Docker 基础命令，比如说下载镜像、实例化镜像、使用容器、关闭容器、删除容器\镜像、使用仓库、创建镜像等模块的使用。其他系统应该除了安装外其他操作都可以通用。

# Docker安装
## 明确安装要求
![安装要求]({{site.baseurl}}/assets/img/docker/安装要求.png)

这里安装机器为win11企业版工作站，在安装上没有什么问题。

## 确认硬件支持情况
![验证兼容性]({{site.baseurl}}/assets/img/docker/验证兼容性.png)

如果需要查询intel cpu支持情况的请直接上ark，这里安装机器已经开启。

## 安装环境部署
在主板的bios设置中打开虚拟化支持。

在设置-应用-程序和功能-启用或关闭windows功能中选中Hyper-V，重启后基本完成安装环境的部署。

![开启hyper-V]({{site.baseurl}}/assets/img/docker/开启hyper-V.png)

## docker-desktop的下载
我们可以在docker的[官网](https://www.docker.com/products/docker-desktop/)下载windows桌面版本的docker软件。

![docker官网]({{site.baseurl}}/assets/img/docker/docker官网.png)

## 安装应用
![安装docker1]({{site.baseurl}}/assets/img/docker/安装docker1.png)

![安装docker2]({{site.baseurl}}/assets/img/docker/安装docker2.png)

没有其他什么需要注意的，直接双击安装包一直下一步安装即可。

![安装成功]({{site.baseurl}}/assets/img/docker/安装成功.png)

安装成功后直接点击重启即可。

## 解决运行报错
重启后如果docker自启动了，就会出现一个docker的Agreement界面，我们点击继续接受协议并继续。

![dockerAgreement界面]({{site.baseurl}}/assets/img/docker/dockerAgreement界面.png)

我们使用win11系统时，当第一次使用Docker Desktop将无法启动，会出现如下的报错：

![启动docker报错]({{site.baseurl}}/assets/img/docker/启动docker报错.png)

使用win11后Docker运行报错，主要报错内容是Microsoft.NET Framework无法连接xxx，其实就是无法连接到wsl2。

根据Docker官方说明，需要升级到最新的wsl2内核和Docker Desktop。

我们关闭正在运行的所有wsl2终端，以管理员身份运行PowerShell或者命令行工具。

输入命令行```wsl --update```用于更新wsl系统，等待出现更新完毕为止。

![更新wsl]({{site.baseurl}}/assets/img/docker/更新wsl.png)

![wsl更新成功]({{site.baseurl}}/assets/img/docker/wsl更新成功.png)

## 启动
更新完成后，我们重新双击docker Desktop应用，静待软件启动完毕后出现如下界面：

![dockerDesktop界面]({{site.baseurl}}/assets/img/docker/dockerDesktop界面.png)

同时，我们在cmd命令行中输入```docker --version```，可以正常查询到docker的版本号，则docker在windows系统上安装完毕。

![dockerversion]({{site.baseurl}}/assets/img/docker/dockerversion.png)

# 基础使用
## 示例
首先我们输入docker run hello-world是否会出现下图所示的提示，如果出现报错，这环境配置可能出现了问题。

ps:这行命令会让docker从官方仓库中拉去hello-world的镜像到本地，并且自动将其实例化成容器。

![示例]({{site.baseurl}}/assets/img/docker/示例.png)

## Docker操作
安装完成Docker后，默认每次开机的时候都会自动启动，但我们也可以手动启动，关闭或者重启Docker。

```
# 启动docker
sudo service docker start
# 重启docker
sudo service docker restart
# 停止docker
sudo service docker stop
```

## 对镜像操作
### 获取当前所有镜像
```
docker image ls
# 或者
docker images
```

| 标签 | 含义 |
|:-------------------:|:-------------------:|
| REPOSITORY | 镜像所在仓库 |
| TAG | 镜像标签 |
| IMAGEID | 镜像ID |
| CREATED | 镜像的创建日期(不是获取该镜像的日期) |
| SIZE | 镜像大小 |

![镜像信息]({{site.baseurl}}/assets/img/docker/镜像信息.png)

### 拉取镜像
除了使用官方的镜像外，我们还可以在仓库中申请一个自己的账号，保存自己制作的进行，或者拉去使用他人的镜像。

```
# 官方镜像
docker image pull 镜像名称 
# 或简写为 
docker pull 镜像名称
# 比如
docker pull ubuntu
docker pull ubuntu:16.04

# 个人镜像
docker pull 仓库名称/镜像名称
docker pull xunmi/django

# 第三方仓库拉去
docker pull 第三方仓库地址/仓库名称/镜像名称
docker pull hub.c.163.com/library/mysql:latest
(默认仓库名为library，所有从官方获取镜像相当于`sudo docker image pull library/镜像名称`)
```

![拉取镜像]({{site.baseurl}}/assets/img/docker/拉取镜像.png)

### 删除镜像
```
docker image rm 镜像名或镜像ID 或 docker rmi 镜像名或镜像ID
docker image rm hello-world
docker rmi 9e64176cd8a2
```

删除镜像的前提是没有使用这个镜像的容器，如果有需要先删除容器(报错:Error response from daemon: conflict: unable to delete 镜像ID (must be forced) - image is being used by stopped container 容器ID则代表有容器使用了此镜像。)

可以尝试先执行docker rm 容器ID删除容器，如果还报错，可以看我下方删除容器的具体方法。

几条删除命令的区别:

```
docker rm # 删除一个或多个容器
docker rmi # 删除一个或多个镜像
docker prune # 用来删除不再使用的 docker 对象
```

具体区别可以看下[此博客](https://blog.csdn.net/weixin_30782293/article/details/96616836)

![删除镜像]({{site.baseurl}}/assets/img/docker/删除镜像.png)

### 加载镜像
镜像只是一个只读类型的文件，而我们的环境不可能只是一个这样的文件，所以我们需要把这个镜像加载成我们的环境，也就是让他变成容器。

```
docker run [可选参数] 镜像名 [向启动容器中传入的命令]
```

| 常用可选参数 | 作用 |
|:-------------------:|:-------------------:|
| -i | 以《交互模式》运行容器 |
| -d | 会创建一个守护式容器在后台运行(这样创建容器后不会自动登录容器) |
| -t | 容器启动后会进入其命令行。加入这两个参数后，容器创建就能登录进去。即分配一个伪终端 |
| --name | 为创建的容器命名。(默认会随机给名字，不支持中文字符!!!) |
| -v | 表示目录映射关系，即宿主机目录:容器中目录。注意:最好做目录映射，在宿主机上做修改，然后共享到容器上 |
| -p | 表示端口映射，即宿主机端口:容器中端口。 比如:-p 8080:80 就是将容器中的80端口，映射到主机中的8080端口 |
| -network=host | 表示将主机的网络环境映射到容器中，使容器的网络与主机相同。每个 Docker 容器都有自己的网络连接空间连接到虚拟 LAN。使用此命令则会让容器和主机共享一个网络空间 |

可以启动一个系统docker run -i -d -t --name=kali-test kalilinux/kali-rolling(这里我使用linux的一个发行版kali作为介绍)
或可以简写为docker run -idt --name=kali-test kalilinux/kali-rolling

PS: 如果加载一个我们没有的镜像，docker会自动从官方仓库中进行拉取。

![启动容器1]({{site.baseurl}}/assets/img/docker/启动容器1.png)

或者我们可以启动一个网站docker run -dp 8080:80 --name docker-test docker/getting-started。

![启动容器2]({{site.baseurl}}/assets/img/docker/启动容器2.png)

如果你没有下载docker/getting-started的进行，这里会自动帮你下载。

成功启动后，可以在浏览器中输入```http://localhost:8080/```即可看到如下页面。

![启动容器3]({{site.baseurl}}/assets/img/docker/启动容器3.png)

## 启动交互式容器
上面我们成功将镜像变成了容器，但上述的命令中我们都加入了-d，让容器在后台运行了。下面我们就来讲如何和正在运行的容器进行交互。

### 查看容器
查看容器主要会用到ps命令。

```
# 查看当前所有正在运行的容器
docker ps
# 查看当前所有的容器
docker ps -a
# 使用过滤器(除了name外，常用的还可以指定id:id= 、所有停止的容器:status=exited，正在运行的容器:status=running 等)
docker ps -f name=指定的名字
# 显示2个上次创建的容器(2可以改变)
docker ps -n 2
# 显示最新创建的容器（包括所有状态）
docker ps -l
# 仅显示ip
docker ps -q
 # 显示容器大小
docker ps -s
```

| 标签	 | 含义 |
|:-------------------:|:-------------------:|
| CONTAINER ID | 镜像ID |
| IMAGE	 | 创建容器的镜像名称 |
| COMMAND | 默认启动命令(启动时会自动执行) |
| CREATED | 创建容器的日期 |
| STATUS | 当前的状态(启动了多久，多久之前退出等) |
| PORTS	 | 映射的端口 |
| NAMES | 容器的名称 |
| SIZE  | 容器大小(使用-s命令参数时才能看到) |

![容器状态]({{site.baseurl}}/assets/img/docker/容器状态.png)

### 启动和关闭容器
```
# 停止容器
docker container stop 容器名或容器id
# 或可简写为
docker stop 容器名或容器id

# 强制关闭容器
docker container kill 容器名或容器id
# 或可简写为
docker kill 容器名或容器id

# 启动容器
docker container start 容器名或容器id
# 或可简写为
docker start 容器名或容器id
```

如果我们成功启动或者关闭一个容器的话，会返回容器名或者容器id。

stop和kill的区别: stop是比较优雅的关掉一个容器，类似我们正常退出一个软件，而kill是当一个进程出现意外无法正常关闭的时候，我们强行进行关闭，有点像我们使用任务管理器进行结束进程操作。

![进程关闭]({{site.baseurl}}/assets/img/docker/进程关闭.png)

### 操作后台容器
之前我们下过一个kali并且放在了后台运行，如果没有下载可以执行下列命令(docker run -i -d -t --name=kali-test kalilinux/kali-rolling)，下面我们就用下面的命令开启kali。

首先我们确保我们要进入的容器是开启状态的，使用docker ps -a查看其中的STATUS属性是否是Up开头，如果不是先照着上面启动容器的方法开启容器。

我们开启容器后，如果需要在容器内执行命令，可以将后台切换到前台，也可能使用docker命令将我们需要执行的命令传入。

操作方法有很多种，这里我们介绍一些比较常用的方法。

```
# 如果我只需要执行简单的一两条命令可以使用docker exec
# 执行单条命令 (-i: 启动并且保留交互式命令行; -t:作用是分配一个虚拟的终端; docker run )
docker exec -it 容器名或容器id 执行的命令
# 比如
docker exec -it kali-test whoami
# 用这种方法，我们还可以启动命令行，根据Linux的特性，系统程序会在/bin中，linux中常用的Shell有多个，其中大部分用的Linux默认的为bash
# 所以我们启动命令可以自行如下命令(除了/bin/bash外，linux一般还会带/bin/sh、/bin/rbash、/bin/dash等，具体区别可以自行百度)
docker exec -it 容器名或容器id /bin/bash
# 比如
docker exec -it kali-test /bin/bash
# 除了exec外还有attach可以使用，但它有个弊端，多终端启动attach后，都会会同步显示。如果有一个窗口阻塞了，那么其他窗口也无法再进行操作。
docker attach 容器名或容器id
# 比如
docker attach kali-test
```

| exec可选参数 | 作用 |
|:-------------------:|:-------------------:|
| -d | 会创建一个守护式容器在后台运行(这样创建容器后不会自动登录容器) |
| -e | 设置环境变量 |
| -i | 表示以《交互模式》运行容器 |
| -t | 表示容器启动后会进入其命令行。加入这两个参数后，容器创建就能登录进去。即分配一个伪终端 |
| -u | 设置用户名和UID |
| -w | 设置容器内的工作目录 |

![exec连接容器]({{site.baseurl}}/assets/img/docker/exec连接容器.png)

除了上述方法外，在进入容器后，我们还可以尝试安装SSH或者nsenter尝试登陆，不过这两种方法都先进入容器安装后才能使用。

```
# nsenter安装步骤
wget https://www.kernel.org/pub/linux/utils/util-linux/v2.24/util-linux-2.24.tar.gz  
tar -xzvf util-linux-2.24.tar.gz  
cd util-linux-2.24/  
./configure --without-ncurses  
make nsenter  
sudo cp nsenter /usr/local/bin  
```

### 删除容器
如我我们需要删除一个容器，首先需要确保这个容器已经停止了，因为正在运行的容器是无法直接删除。

我们可以运行一下docker ps -a，如果发现没有停止，可以使用docker stop停止(STATUS下已Exited开头则是停止的)

![删除前容器状态]({{site.baseurl}}/assets/img/docker/删除前容器状态.png)

```
# 使用rm删除容器
docker rm 容器名或容器id
# 列如
docker rm docker-test
```

如果报错Error response from daemon: You cannot remove a running container 容器ID. Stop the container before attempting removal or force remove则代表这个容器已经启动，需要执行 docker stop 容器id，停止此容器。

## 容器制作成镜像
我们为什么要把容器制作成镜像?

· 镜像可以看作为是一种备份，如果我们后期环境出现了问题，可以还原到早期镜像。

· 镜像便于传播，可以让自己的其他设备或他人的重复利用变得更加简单容易。

```
# 将容器制作成镜像
docker commit 容器名 镜像名
# 镜像打包备份(打包备份的文件会自动存放在当前命令行的路径下，如果想让保存的文件可以打开，可以加.tar后缀)
docker save -o 保存的文件名 镜像名
# 镜像解压
docker load -i 文件路径/备份文件
```

我们将打包备份的镜像可以通过网络发送到其他设备上，使用docker镜像解压即可直接使用你的环境。

## Dashboard
Docker 仪表板的主要作用为:快速访问容器日志，启动容器的 shell，并轻松管理容器生命周期（停止、删除等）。

![dockerdesktop]({{site.baseurl}}/assets/img/docker/dockerdesktop.png)

## 容器远程连接
这里我们以ubuntu系统为例。

我们首先使用一个ubuntu镜像运行为容器，执行以下命令，让容器在后台运行。

```
docker run -itd --name docker-ubuntu-root -v /C/Users/mcc/Desktop/docker-ubuntu-root:/root -p 9000:22 ubuntu /bin/bash
```

进入我们的容器中，首先更新一下系统所需的基础指令，以便我们后续软件的安装。

使用```apt-get install openssh-sever```安装ssh服务。

配置/etc/ssh/sshd_config文件，将里面PermitRootLogin后面的内容改成yes。

![PermitRootLogin]({{site.baseurl}}/assets/img/docker/PermitRootLogin.png)

使用```/etc/init.d/ssh start```启动我们的ssh服务。

然后我们就可以使用ssh指令远程直接连接我们的docker容器了，就像正常的服务器一样使用。

![connection]({{site.baseurl}}/assets/img/docker/connection.png)

<br />
## 参考资料
https://post.smzdm.com/p/a78zvw9l/

https://blog.csdn.net/chenthe1/article/details/129463675

https://www.docker.org.cn/index.html

https://www.kancloud.cn/jingyucloud/docker/216412

https://hub.docker.com/

https://hub.daocloud.io/

https://c.163yun.com/hub#/home

https://blog.csdn.net/weixin_30782293/article/details/96616836

https://blog.csdn.net/qq_39611230/article/details/108641842

https://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html

https://blog.csdn.net/hwijew/article/details/88171749

https://ubuntu.com/server/docs/service-openssh

https://blog.csdn.net/fanhuaIIIIII/article/details/10862645
