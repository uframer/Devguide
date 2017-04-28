# Arch及CentOS发行版指南

## USB设备配置

Linux用户需要手动为JTAG适配器添加USB访问权限。

> **Note** 对于Arch Linux：请将下面命令中的`plugdev`替换为`uucp`。

请通过`sudo`运行下面的命令：

<div class="host-code"></div>

```sh
cat > $HOME/rule.tmp <<_EOF
# All 3D Robotics (includes PX4) devices
SUBSYSTEM=="usb", ATTR{idVendor}=="26AC", GROUP="plugdev"
# FTDI (and Black Magic Probe) Devices
SUBSYSTEM=="usb", ATTR{idVendor}=="0483", GROUP="plugdev"
# Olimex Devices
SUBSYSTEM=="usb",  ATTR{idVendor}=="15ba", GROUP="plugdev"
_EOF
sudo mv $HOME/rule.tmp /etc/udev/rules.d/10-px4.rules
sudo /etc/init.d/udev restart
```

你需要把当前用户添加到`plugdev`组里：

<div class="host-code"></div>

```sh
sudo usermod -a -G plugdev $USER
```

## 不常见Linux系统的安装指南

### CentOs

构建过程需要使用Python 2.7.5。目前我们以Centos 7做例子。（对于之前版本的Centos，可能并行安装一个v2.7.5版本的Python，但是这样做可能会破坏yum的功能，所以我们不推荐你这么做。）

你需要添加EPEL这个仓库以便安装`openocd`、`libftdi-devel`、`libftdi-python`：

<div class="host-code"></div>

```sh
wget https://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
sudo yum install epel-release-7-5.noarch.rpm
yum update
yum groupinstall “Development Tools”
yum install python-setuptools
easy_install pyserial
easy_install pexpect
yum install openocd libftdi-devel libftdi-python python-argparse flex bison-devel ncurses-devel ncurses-libs autoconf texinfo libtool zlib-devel cmake
```

注意：你可能也需要安装python-pip和screen。

#### 额外的32位库

请使用如下命令检测是否正确安装ARM工具链：

<div class="host-code"></div>

```sh
arm-none-eabi-gcc --version
```

如果看到类似下面这样的消息：

<div class="host-code"></div>

```sh
bash: gcc-arm-none-eabi-4_7-2014q2/bin/arm-none-eabi-gcc: /lib/ld-linux.so.2: bad ELF interpreter: No such file or directory
```
那么你需要再安装32位的glibc.i686和ncurses-libs.i686库：

<div class="host-code"></div>

```sh
sudo yum install glibc.i686 ncurses-libs.i686
```
> **Note** 安装ncurses-libs.i686会连带着安装绝大多少我们需要的32位库。Centos 7已经自带了对大多数PX4相关设备的支持，因此一般不需要再添加udev规则。这些设备可以通过预定义的`dialout`组访问，所以你需要把当前用户添加到`dialout`组中。

### Arch Linux

<div class="host-code"></div>

```sh
sudo pacman -S base-devel lib32-glibc git-core python-pyserial zip python-empy
```

安装[yaourt](https://wiki.archlinux.org/index.php/Yaourt#Installation)，它是[Arch User Repository (AUR)](https://wiki.archlinux.org/index.php/Arch_User_Repository)的包管理器。

然后再用yaourt下载、编译并安装`genromfs`：

<div class="host-code"></div>

```sh
yaourt -S genromfs
```

#### 权限

当前用户需要加入到`uucp`组中：

<div class="host-code"></div>

```sh
sudo usermod -a -G uucp $USER
```

然后，你需要重新登录以便使修改生效。

> **Note** **一定要重新登录，否则不会生效！而且也要重新插拔设备！**

### 安装工具链

执行下面的命令安装GCC 4.9或者5.4：

**GCC 4.9**:

```sh
pushd .
cd ~
wget https://launchpad.net/gcc-arm-embedded/4.9/4.9-2015-q3-update/+download/gcc-arm-none-eabi-4_9-2015q3-20150921-linux.tar.bz2
tar -jxf gcc-arm-none-eabi-4_9-2015q3-20150921-linux.tar.bz2
exportline="export PATH=$HOME/gcc-arm-none-eabi-4_9-2015q3/bin:\$PATH"
if grep -Fxq "$exportline" ~/.profile; then echo nothing to do ; else echo $exportline >> ~/.profile; fi
. ~/.profile
popd
```

**GCC 5.4**:

```sh
pushd .
cd ~
wget https://launchpad.net/gcc-arm-embedded/5.0/5-2016-q2-update/+download/gcc-arm-none-eabi-5_4-2016q2-20160622-linux.tar.bz2
tar -jxf gcc-arm-none-eabi-5_4-2016q2-20160622-linux.tar.bz2
exportline="export PATH=$HOME/gcc-arm-none-eabi-5_4-2016q2/bin:\$PATH"
if grep -Fxq "$exportline" ~/.profile; then echo nothing to do ; else echo $exportline >> ~/.profile; fi
. ~/.profile
popd
```

**如果使用的是Debian Linux，请运行如下命令：**

<div class="host-code"></div>

```sh
sudo dpkg --add-architecture i386
sudo apt-get update
```

**然后安装32位支持库** （如果你的系统就是32位的，那么下面的命令可能会出错，可以忽略这个错误）：

<div class="host-code"></div>

```sh
sudo apt-get install libc6:i386 libgcc1:i386 libstdc++5:i386 libstdc++6:i386
sudo apt-get install gcc-4.6-base:i386
```

## Ninja构建系统

Ninja比Make更快，PX4的CMake支持生成Ninja脚本。不幸的是，Ubuntu自带的Ninja版本非常旧。你可以按照下面的操作安装[Ninja](https://github.com/martine/ninja)：

<div class="host-code"></div>

```sh
mkdir -p $HOME/ninja
cd $HOME/ninja
wget https://github.com/martine/ninja/releases/download/v1.6.0/ninja-linux.zip
unzip ninja-linux.zip
rm ninja-linux.zip
exportline="export PATH=$HOME/ninja:\$PATH"
if grep -Fxq "$exportline" ~/.profile; then echo nothing to do ; else echo $exportline >> ~/.profile; fi
. ~/.profile
```

## 常见问题

### 版本测试

输入如下命令：

<div class="host-code"></div>

```sh
arm-none-eabi-gcc --version
```

这个命令的输出应该类似下面这个样子：

<div class="host-code"></div>

```sh
arm-none-eabi-gcc (GNU Tools for ARM Embedded Processors) 4.7.4 20140401 (release) [ARM/embedded-4_7-branch revision 209195]
Copyright (C) 2012 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

如果输出类似下面这样：

<div class="host-code"></div>

```sh
arm-none-eabi-gcc --version
arm-none-eabi-gcc: No such file or directory
```

请检查你是否在前面的步骤中正确安装了32位库。
