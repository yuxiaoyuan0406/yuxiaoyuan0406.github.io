---
layout: article
title: 【IC】在Ubuntu 22.04上安装并使用virtuoso IC618
permalink: /article/:title.html
key: virtuoso-on-ubuntu
tags: 
  - Linux
  - IC
author: Yu Xiaoyuan
show_author_profile: true
license: WTFPL
---

<!-- abstract begin -->
Cadence Virtuoso IC618工具集是设计数字、模拟集成电路的常用工具。
但官方推荐使用的系统版本较低，使用起来并不方便。
本文主要记录了在Ubuntu 22.04系统上安装使用IC618和一些其他常用工具的过程，仅供学习参考使用。
<!-- abstract end -->

<!--more-->

<!-- begin include -->
{%- include general-variables.html -%}
<!-- end include -->

<!-- begin private variable of Liquid -->

<!-- {%- increment equation-h2-1 -%} -->
<!-- end private variable of Liquid -->

## 开始之前

**由于兼容性问题，不建议将软件安装到Ubuntu 22，本文内容仅作展示和参考。**
{:.warning}

**本文所记录的安装过程，仅用于学习交流使用，请支持正版软件。**
{:.info}

## 操作系统和软件列表

本文中使用的操作系统为Ubuntu Server 22.04.5，系统镜像请移步[Ubuntu官网](https://ubuntu.com/)。

准备安装的软件列表：

- IC618
- Spectre 21
- calibre 2022

[Cadence工具包下载](https://pan.baidu.com/s/197krGnTmSO-MCkLcYDafRA?pwd=pb9x)**仅供学习使用**。

## 环境依赖

```sh
sudo apt -y install openjdk-11-jdk
sudo apt -y install ksh
sudo apt -y install csh
sudo apt -y install xterm
sudo apt -y install lsb-core libc6-i386 libc6-dev-i386
sudo apt install -y db5.3-util elfutils expat gdb ksh libapr1-dev libaprutil1-dev libc6 libc6-dev libgl1-mesa-dri libjpeg-turbo8-dev libmotif-dev libncursesw5-dev libpng-dev libssl-dev libxi6 libxss-dev libxtst6 lsb mesa-utils openjdk-17-jdk tcsh xfonts-75dpi xfonts-bolkhov-misc xterm xvfb libglu1-mesa-dev
sudo add-apt-repository ppa:linuxuprising/libpng12
sudo apt update
sudo apt -y install libpng12-0
sudo apt -y install libjpeg62
sudo apt -y install libncurses5
sudo ln -sf /lib/x86_64-linux-gnu/libdl.so.2 /usr/lib/x86_64-linux-gnu/libdl.so
```

## 安装位置

我们将Cadence安装在`/opt/cadence`目录下。

## InstallScape

InstallScape是Cadence的安装工具。
首先将`Base_IC06.18.000`目录下的IC618安装包解压，在解压后的文件中找到`IScape04.23-s012lnx86.t.Z`，将其拷贝到`/opt/cadence/InstallScape`下并解压：

```bash
tar -xvzf IScape04.23-s012lnx86.t.Z
```

解压后将获得下面这样一些文件：

```
.
├── data
│   └── ...
├── iscape -> iscape.04.23-s012
├── iscape.04.23-s012
│   ├── bin
│   │   ├── iscape.exe
│   │   ├── iscape.sh
│   │   └── sl2kti.pl
│   ├── docs
│   │   └── ...
│   ├── lib
│   │   └── ...
│   ├── MANIFEST.lnx86
│   ├── README
│   ├── runtime
│   │   └── LNX86
│   └── VERSION.lnx86
└── IScape04.23-s012lnx86.t.Z

8 directories, 44 files
```

我们需要执行这里面的安装脚本`iscape.sh`。

```bash
./iscape/bin/iscape.sh
```

启动后将看见这样一个界面：

![InstallScape]({{ image_dir }}/InstallScape.png "InstallScape主界面")

## IC618

在安装界面的Local directory/Media install选项中选择安装包路径
`Base_IC06.18.000/IC06.18.000_lnx86.Base/CDROM1`。

继续之后，选择安装路径
`/opt/cadence/IC618`

![InstallScape IC618]({{ image_dir }}/InstallScape-IC618.png "安装IC618的界面")

或者这里可以选择自己喜欢的路径，但这个安装路径后面需要用来配置启动脚本，所以需要自己明确。

安装过程中如果出现配置界面，出现的和 license、OA 有关的选项全选 no。
{:.warning}

### 修改Cadence的检测脚本

安装完成之后需要修改cadence自己的系统监测文件`sysname`，笔者这里的路径为`/opt/cadence/IC618/share/oa/bin/sysname`。

从文件209行开始是一段系统检查的指令，原内容大致如下：

```bash
...
                4.4.*)
                  # SLES 12 SP2
                  sysname=`whichCompiler $linuxName`
                  sysnames="$sysname `whichCompiler $otherName` $sysnames";;
                * )
                  check_global;;
            esac;;
        ppc64*)
...
```

做如下修改，将ubuntu 22添加到支持列表。这里需要注意`5.*)`这一行，该值应该由系统的内核版本决定。这个值可以通过指令`uname -r`查看。

```bash
...
                4.4.*)
                  # SLES 12 SP2
                  sysname=`whichCompiler $linuxName`
                  sysnames="$sysname `whichCompiler $otherName` $sysnames";;
                5.*)
                  # ubuntu22
                  sysname=`whichCompiler $linuxName`
                  sysnames="$sysname `whichCompiler $otherName` $sysnames";;
                * )
                  check_global;;
            esac;;
        ppc64*)
...
```

### 为IC618添加环境变量

安装cadence的过程中，安装软件并没有自动将路径添加到环境变量，需要我们手动添加。

为了方便管理，我们将cadence相关的环境变量用一个统一的文件管理`/etc/profile.d/cadence_env.sh`。首先创建文件并赋予执行权限：

```bash
sudo touch /etc/profile.d/cadence_env.sh
sudo chmod 644 /etc/profile.d/cadence_env.sh
```

之后在该文件中加入如下内容：

```bash
#!/bin/bash

# cadence
export CadenceDir=/opt/cadence
export CADHOME=$CadenceDir
export CDS=$CADHOME/IC618
export LANG=C

## netlisting mode
export CDS_Netlisting_Mode=Analog

## search order for .cdsenv
# export CDS_LOAD_ENV=CWD

## Browser setup for Cadence Help
export MOZILLA_HOME=/usr/bin/firefox

## Other tools may need this
export CDSDIR=$CDS
export CDSHOME=$CDS
export CADENCE_DIR=$CDS
export CDS_INIT_DIR=$CDS
export CDS_ROOT=$CDS
export CDSROOT=$CDS

# ic618
export IC_HOME=$CadenceDir/IC618
export PATH=$PATH:$IC_HOME/tools/dfII/bin:$IC_HOME/tools/bin:$IC_HOME/bin
export W3264_NO_HOST_CHECK=1
```

### patch IC618

在下载的文件中找到`ocad.tar.gz`，将其拷贝到`/opt`下并解压

```bash
tar -xvzf ocad.tar.gz
```

首先可以查看ocad工具的使用说明：

```
Usage :
    1patch V0.3
    ./1patch -options installDirectory
         options:
             [-noecc ] : Used for tools without ECC check patch
             [-ecc ]   : Used for tools with ECC check patch
             [-jre ]   : Used for java patch
         InstallDirectory:
             The tools you want to patch.
         Example:
            ./1patch.sh -ecc /software/syn2019
```

根据使用说明，打补丁使用如下指令：

```bash
/opt/ocad/bin/1patch -ecc /opt/cadence/IC618/
```

完成之后制作license，根据ocad工具作者的介绍，说明文件在`/opt/ocad/tools/licsrv/generator/cdslic.gen`下，按照说明的内容，我们需要替换license中的的`HOSTNAME`和`YOURHOSTID`。
在命令行输入指令`hostname`和`hostid`可以分别获得这两个值。

随后将这两个值替换到license中，license位于下载的文件列表中的`cadence.dat`。

修改license文件的第二行，将license服务程序的地址写进入，这里笔者将`/ocad`置于`/opt`下，所以这里填的路径为：`/opt/ocad/tools/licsrv/bin/cdslmd`。

修改前：

```
SERVER localhost ANY 20030
DAEMON cdslmd
USE_SERVER
...
```

修改后：

```
SERVER HOSTNAME YOURHOSTID 20030
DAEMON cdslmd /opt/ocad/tools/licsrv/bin/cdslmd
USE_SERVER
...
```

修改完成后保存license文件到`/opt/ocad/tools/licsrv/licenses/cds.license.dat`。

之后将license的路径添加到我们的环境变量文件。在`/etc/profile.d/cadence_env.sh`中添加如下内容：

```bash
# license
export CDS_LIC_FILE=/opt/ocad/tools/licsrv/licenses/cds.license.dat
```

## SPECTRE21

SPECTRE的安装方法与IC618类似，启动安装软件InstallScape后找到安装包的路径（记得提前解压），设置好安装路径后就可以安装了。

### 修改SPECTRE的检测脚本

文件位于`/opt/cadence/SPECTRE211/share/oa/bin/sysname`。

与修改Cadence的检测脚本类似，添加如下内容：

```bash
                5.*)
                  sysname="ubuntu22"
                  sysnames="$sysname `whichCompiler $otherName` $sysnames";;

```

### 为SPECTRE21添加环境变量

在`/etc/profile.d/cadence_env.sh`中添加如下内容：

```bash
# spectre21
export SPECTRE_HOME=$CadenceDir/SPECTRE211
export PATH=$PATH:$SPECTRE_HOME/tools/dfII/bin:$SPECTRE_HOME/tools/bin:$SPECTRE_HOME/bin
```

### patch SPECTRE21

```bash
/opt/ocad/bin/1patch -ecc /opt/cadence/SPECTRE211/
```

## Calibre2022

将下载的文件列表中`calibre2022/mentor.tar.gz`拷贝到`/opt/mentor.tar.gz`，解压：

```bash
tar -xvf mentor.tar.gz
```

### 为Calibre2022添加环境变量

在`/etc/profile.d/cadence_env.sh`中添加如下内容：

```bash
# mentor
export MGC_HOME=/opt/mentor
export MGC_TMPDIR=/tmp
# calibre
export CALIBRE_HOME=$MGC_HOME/calibre2022.2/aoj_cal_2022.2_38.20
export MGLS_LICENSE_FILE=$MGC_HOME/license/license.dat
export MGC_LICENSE_FILE=$MGC_HOME/license/license.dat
export PATH=$PATH:$CALIBRE_HOME/bin
export MGC_LIB_PATH=$CALIBRE_HOME/lib

export USE_CALIBRE_VCO=aoj
```

### 为Calibre2022修改系统默认shell

Calibre中shell脚本的语法并不被Ubuntu的dash所支持。这里我们使用下面这个指令将其替换为bash：

```bash
sudo dpkg-reconfigure dash
```

然后就可以在命令行中测试calibre的gui：

```bash
calibre -gui
```

## 汇总环境变量

上面的安装过程中我们反复修改了环境变量，这里整理整个文件如下：

```bash
#!/bin/bash

# cadence
export CadenceDir=/opt/cadence
export CADHOME=$CadenceDir
export CDS=$CADHOME/IC618
export LANG=C
export PATH=$PATH:

## netlisting mode
export CDS_Netlisting_Mode=Analog

## search order for .cdsenv
# export CDS_LOAD_ENV=CWD

## Browser setup for Cadence Help
export MOZILLA_HOME=/usr/bin/firefox

## Other tools may need this
export CDSDIR=$CDS
export CDSHOME=$CDS
export CADENCE_DIR=$CDS
export CDS_INIT_DIR=$CDS
export CDS_ROOT=$CDS
export CDSROOT=$CDS

# ic618
export IC_HOME=$CadenceDir/IC618
export PATH=$PATH:$IC_HOME/tools/dfII/bin:$IC_HOME/tools/bin:$IC_HOME/bin
export W3264_NO_HOST_CHECK=1

# license
export CDS_LIC_FILE=/opt/ocad/tools/licsrv/licenses/cds.license.dat

# spectre21
export SPECTRE_HOME=$CadenceDir/SPECTRE211
export PATH=$PATH:$SPECTRE_HOME/tools/dfII/bin:$SPECTRE_HOME/tools/bin:$SPECTRE_HOME/bin

# mentor
export MGC_HOME=/opt/mentor
export MGC_TMPDIR=/tmp
# calibre
export CALIBRE_HOME=$MGC_HOME/calibre2022.2/aoj_cal_2022.2_38.20
export MGLS_LICENSE_FILE=$MGC_HOME/license/license.dat
export MGC_LICENSE_FILE=$MGC_HOME/license/license.dat
export PATH=$PATH:$CALIBRE_HOME/bin
export MGC_LIB_PATH=$CALIBRE_HOME/lib

export USE_CALIBRE_VCO=aoj
```

## 远程访问

安装服务器的目的是多用户同时访问。
本节内容旨在说明如何配置用户的个人电脑实现局域网内部的远程访问。

### 服务器端的sshd设置

考虑到安全、X11转发（图形界面转发）等需求，需要在`/etc/ssh/sshd_config`中做如下配置：

```config
PermitEmptyPasswords no
X11Forwarding yes
X11UseLocalhost yes
```

上面的配置禁用了无密码登录，这样能防止登录root账号；启用了X11转发。

### 客户端电脑的设置

客户端电脑需要安装X服务器，Linux一般自带，macOS和Windows需要自行安装。

Windows上的免费X服务器方案是[MobaXterm](https://mobaxterm.mobatek.net/)。

客户端电脑需要设置用户个人目录下`.ssh/config`文件，在其中配置ssh访问服务器时的行为。
我们需要设置服务器的别名、地址（或者域名）、ssh连接端口、用户名、X11转发、身份校验方式。

```config
Host mems-serv
    HostName 192.168.1.99
    Port 22
    User dennis
    ForwardX11 yes
    ForwardX11Trusted yes
    IdentitiesOnly yes
```

除了上述的ssh设置之外，客户端电脑如果想要实现X11转发，需要设置环境变量`DISPLAY`。
其值应为客户端地址和X服务器监听的端口，一般设置为`localhost:0.0`。

## reference

[Ubuntu22安装IC618、spectre21、innovus211、calibre2022](https://zhuanlan.zhihu.com/p/659144314)  
[Ubuntu 搭建 Cadence 模拟仿真环境](https://zhuanlan.zhihu.com/p/370708944)  
[1patch V0.3](https://bbs.eetop.cn/thread-896342-1-1.html)  
[使用 Windows 自带 ssh 的 X11转发功能并配置 ssh 和 VSCode](https://blog.dengqi.org/posts/%E4%BD%BF%E7%94%A8-windows-%E8%87%AA%E5%B8%A6ssh%E7%9A%84x11%E8%BD%AC%E5%8F%91%E5%8A%9F%E8%83%BD%E5%B9%B6%E9%85%8D%E7%BD%AEssh%E5%92%8Cvscode/)  
[MobaXterm](https://mobaxterm.mobatek.net/)  
