---
title: "conda_create"
cover: "/img/conda-create/cover.webp"
pubDatetime: 2024-12-08T14:49:46.000Z
modDatetime: 2024-12-10T10:03:59.096Z
description: "conda下载没什么需要注意的，也不用按着啥教程来，直接去官网上下载就行，不过这里处于节约内存的考虑，我下载的是 miniconda。相较于anaconda的满血版，miniconda更为轻量级，因为主环境一般也不方…"
tags:
  - conda
  - terminal
  - python
  - 环境配置
---
## conda下载

conda下载没什么需要注意的，也不用按着啥教程来，直接去官网上下载就行，不过这里处于节约内存的考虑，我下载的是 `miniconda`。相较于anaconda的满血版，miniconda更为轻量级，因为主环境一般也不方便使用(可能是我不会用？)，而其他不同版本的环境下的各个包又是相互独立的，所以个人感觉miniconda就已经够用了，毕竟刚重装系统，内存还是要合理规划使用的。

刚开始下载的时候不知道咋回事下成了anaconda，可能是因为下载链接过于隐秘？这里附上miniconda的下载链接：[https://repo.anaconda.com/miniconda/](https://repo.anaconda.com/miniconda/) 。除了配置一下安装的目录位置以及按需将conda加入到PATH环境中，其他的就是一路Enter了。

## 创建一个新的conda环境

### 初始化

创建环境一般需要指定python的版本，好像不指定就是默认？秉持着用双数版本不用最新的原则，我打算创建一个3.10.x版本的conda环境。初始化的命令如下；

```
conda create --name <Env_name> python=<version>
```

一般用梯子创建不会有啥问题，这一步到这里就完成了。

### 下载必要的包

初始化之后可以在终端看到我们仍然在base环境下，想要进入新的环境需要激活它。激活的命令如下：

```
active <Env_name>
```

如果太久没有使用而忘记了Env的名字时，可以直接去miniconda的目录下查找，也可以用如下的命令来查询已有的环境：

```
conda env list
```

有激活当然就有退出命令，虽然我一般直接关闭终端根本用不上这个命令，但还是学一个吧：

```
conda deactivate
```

接下来就按需下载必要的包就可以了，下载包一般会分为pip下载和conda下载，通常情况两种方法都是可以，但是有时只有pip有用，不过也有一些情况用pip下载的包会和其他的包冲突，欸！环境配置中的包冲突问题一直都是一个玄学问题。下载包的命令格式如下：

```
conda install <package_name>

pip install <package_name>
```

下载包的时候，常常会出现网络问题，毕竟人家的服务器在海外，如果遇到这种情况可以考虑使用镜像地址下载，只需要在后边加上如下的命令就可以了：

```
conda install <package_name> -c https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
```

检查包是否下载也是某些情况下必要的操作，这里也附上代码：

```
conda list

pip list

conda list <package_name>

pip list <package_name>
```
