# Linux开发环境

我们将Debian/Ubuntu LTS作为默认支持的Linux发行版，但是我们在[Arch及CentOS发行版指南](starting-installing-linux-boutique.md)里也提供了针对Cent OS和Arch Linux的指南。

## 权限设置

> **警告** Never ever fix permission problems by using 'sudo'. It will create more permission problems in the process and require a system reinstallation to fix them.

当前用户需要属于`dialout`组：

```sh
sudo usermod -a -G dialout $USER
```

随后，你需要登出再登入，因为这个改动只会在下一次登入时生效。

## 安装

Update the package list and install the following dependencies for all PX4 build targets. PX4 supports four main families:

* NuttX based hardware: [Pixhawk](hardware-pixhawk.md), [Pixfalcon](hardware-pixfalcon.md),
  [Pixracer](hardware-pixracer.md), [Crazyflie](hardware-crazyflie2.md),
  [Intel Aero](hardware-intel-aero.md)
* Snapdragon Flight hardware: [Snapdragon](hardware-snapdragon.md)
* Linux-based hardware: [Raspberry Pi 2/3](hardware-rpi.md), Parrot Bebop
* Host simulation: [jMAVSim SITL](simulation-sitl.md) and [Gazebo SITL](simulation-gazebo.md)

> **信息** Install the [Ninja Build System](http://dev.px4.io/starting-installing-linux-boutique.html#ninja-build-system) for faster build times than with Make. It will be automatically selected if installed.

```sh
sudo add-apt-repository ppa:george-edison55/cmake-3.x -y
sudo apt-get update
sudo apt-get install python-argparse git-core wget zip \
    python-empy qtcreator cmake build-essential genromfs -y
# simulation tools
sudo apt-get install ant protobuf-compiler libeigen3-dev libopencv-dev openjdk-8-jdk openjdk-8-jre clang-3.5 lldb-3.5 -y
```

### 采用NuttX系统的硬件

Ubuntu comes with a serial modem manager which interferes heavily with any robotics related use of a serial port \(or USB serial\). It can deinstalled without side effects:

```sh
sudo apt-get remove modemmanager
```

Update the package list and install the following dependencies. Packages with specified versions should be installed with this particular package version.

```sh
sudo apt-get install python-serial openocd \
    flex bison libncurses5-dev autoconf texinfo build-essential \
    libftdi-dev libtool zlib1g-dev \
    python-empy  -y
```

Make sure to remove leftovers before adding the arm-none-eabi toolchain.

```sh
sudo apt-get remove gcc-arm-none-eabi gdb-arm-none-eabi binutils-arm-none-eabi gcc-arm-embedded
sudo add-apt-repository --remove ppa:team-gcc-arm-embedded/ppa
```

Then follow the [toolchain installation instructions](http://dev.px4.io/starting-installing-linux-boutique.html#toolchain-installation) to install the arm-none-eabi toolchain version 4.9 or 5.4 manually.

### Snapdragon Flight

#### 工具链安装

```sh
sudo apt-get install android-tools-adb android-tools-fastboot fakechroot fakeroot unzip xz-utils wget python python-empy -y
```

```sh
git clone https://github.com/ATLFlight/cross_toolchain.git
```

Get the Hexagon SDK 3.0 from QDN: [https://developer.qualcomm.com/download/hexagon/hexagon-sdk-v3-linux.bin](https://developer.qualcomm.com/download/hexagon/hexagon-sdk-v3-linux.bin)

This will require a QDN login. You will have to register if you do not already have an account.

Now move the following files in the download folder of the cross toolchain as follows:

```sh
mv ~/Downloads/hexagon-sdk-v3-linux.bin cross_toolchain/downloads
```

Install the toolchain and SDK like this:

```sh
cd cross_toolchain
./installv3.sh
cd ..
```

Follow the instructions to set up the development environment. If you accept all the install defaults you can at any time re-run the following to get the env setup. It will only install missing components.

After this the tools and SDK will have been installed to "$HOME/Qualcomm/...". Append the following to your ~/.bashrc:

```sh
export HEXAGON_SDK_ROOT="${HOME}/Qualcomm/Hexagon_SDK/3.0"
export HEXAGON_TOOLS_ROOT="${HOME}/Qualcomm/HEXAGON_Tools/7.2.12/Tools"
export PATH="${HEXAGON_SDK_ROOT}/gcc-linaro-4.9-2014.11-x86_64_arm-linux-gnueabihf_linux/bin:$PATH"
```

Load the new configuration:

```sh
source ~/.bashrc
```

#### Sysroot安装

A sysroot is required to provide the libraries and header files needed to cross compile applications for the Snapdragon Flight applications processor.

The qrlSDK sysroot provies the required header files and libraries for the camera, GPU, etc.

Download the file [Flight\_3.1.1\_qrlSDK.zip](http://support.intrinsyc.com/attachments/download/690/Flight_3.1.1_qrlSDK.zip) and save it in `cross_toolchain/download/`.

```sh
cd cross_toolchain
unset HEXAGON_ARM_SYSROOT
./qrlinux_sysroot.sh
```

Append the following to your ~/.bashrc:

```sh
export HEXAGON_ARM_SYSROOT=${HOME}/Qualcomm/qrlinux_v3.1.1_sysroot
```

Load the new configuration:

```sh
source ~/.bashrc
```

For more sysroot options see [Sysroot Installation](https://github.com/ATLFlight/cross_toolchain/blob/sdk3/README.md#sysroot-installation)

#### 更新ADSP固件

Before building, flashing and running code, you'll need to update the [ADSP firmware](advanced-snapdragon.html#updating-the-adsp-firmware).

#### 参考资料

There is a an external set of documentation for Snapdragon Flight toolchain and SW setup and verification:
[ATLFlightDocs](https://github.com/ATLFlight/ATLFlightDocs/blob/master/README.md)

Messages from the DSP can be viewed using mini-dm.

```sh
${HEXAGON_SDK_ROOT}/tools/debug/mini-dm/Linux_Debug/mini-dm
```

**注意**：你也可以可使用[nano-dm](https://github.com/kevinmehall/nano-dm)（尤其适用于Mac）。

### Raspberry Pi硬件

Raspberry Pi的开发者需要按照下面的指导下载RPi Linux的工具链。安装脚本会自动安装交叉编译工具链。如果你想在RPi上面做*本地*编译，那么请参考[这份文档](http://dev.px4.io/hardware-pi2.html#native-builds-optional)

```sh
git clone https://github.com/pixhawk/rpi_toolchain.git
cd rpi_toolchain
./install_cross.sh
```
在安装过程中，这个脚本可能会需要你提供根用户的密码。

你可以个这个命令传递一个不同的路径，这样就会将工具链安装到你指定的目录中，默认的路径是```/opt/rpi_toolchain```。运行``` ./install_cross.sh <PATH>```。安装脚本会在安装完成后自动配置所需的环境变量（你可能需要重新开启一个终端窗口才能生效）。

最后，运行下面的命令更新环境变量：
```
source ~/.profile
```

### Parrot Bebop

Developers working with the Parrot Bebop should install the RPi Linux Toolchain. Follow the
description under [Raspberry Pi hardware](raspberry-pi-hardware).

Next, install ADB.

``sh
sudo apt-get install android-tools-adb -y` ``

## 下一步

接下来请继续按照[构建PX4软件](starting-building.md)的指导操作。
