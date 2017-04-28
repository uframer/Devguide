# Linux开发环境

我们将Debian/Ubuntu LTS作为默认支持的Linux发行版，但是我们在[Arch及CentOS发行版指南](../setup/dev_env_linux_boutique.md)里也提供了针对Cent OS和Arch Linux的指南。

## 权限设置

> **警告** 一定不要在遇到权限问题时粗暴的通过'sudo'解决。如果滥用sudo可能会造成更大的权限问题，而且很难手动修复，甚至有可能需要你重新安装系统。

当前用户需要属于`dialout`组：

```sh
sudo usermod -a -G dialout $USER
```

随后，你需要登出再登入，因为这个改动只会在下一次登入时生效。

## 安装

更新包列表并安装编译PX4所需的包。PX4支持如下的目标系统：

* 基于NuttX的硬件：[Pixhawk](../flight_controller/pixhawk.md)、[Pixfalcon](../flight_controller/pixfalcon.md)、[Pixracer](../flight_controller/pixracer.md)、[Crazyflie](../flight_controller/crazyflie2.md)、[Intel Aero](./flight_controller/intel_aero.md)
* Snapdragon Flight硬件：[Snapdragon](../flight_controller/snapdragon_flight.md)
* 基于Linux的硬件：[Raspberry Pi 2/3](../flight_controller/raspberry_pi.m)、Parrot Bebop
* 上位机模拟：[jMAVSim SITL](../simulation/sitl.md)和[Gazebo SITL](../simulation/gazebo.md)

> **信息** 安装[Ninja构建系统](../setup/dev_env_linux_boutique.md#ninja-build-system)可以提供比Make更快的构建速度。只要你安装了，PX4就会自动使用它。

```sh
sudo add-apt-repository ppa:george-edison55/cmake-3.x -y
sudo apt-get update
sudo apt-get install python-argparse git-core wget zip \
    python-empy qtcreator cmake build-essential genromfs -y
# simulation tools
sudo apt-get install ant protobuf-compiler libeigen3-dev libopencv-dev openjdk-8-jdk openjdk-8-jre clang-3.5 lldb-3.5 -y
```

### 采用NuttX系统的硬件

Ubuntu自带了一个串口调制解调器管理器，会严重干扰所有使用串口（或者USB串口）的机器人设备。我们需要卸载它消除干扰：

```sh
sudo apt-get remove modemmanager
```

更新软件包列表并安装下面所需的软件包。有些软件包需要安装指定的版本。

```sh
sudo apt-get install python-serial openocd \
    flex bison libncurses5-dev autoconf texinfo build-essential \
    libftdi-dev libtool zlib1g-dev \
    python-empy  -y
```

请确保在安装arm-none-eabi工具链之前移除已有的版本。

```sh
sudo apt-get remove gcc-arm-none-eabi gdb-arm-none-eabi binutils-arm-none-eabi gcc-arm-embedded
sudo add-apt-repository --remove ppa:team-gcc-arm-embedded/ppa
```

请按照[工具链安装指南](../setup/dev_env_linux_boutique.md#toolchain-installation)来手动安装4.9或者5.4版本的arm-none-eabi工具链。

### Snapdragon Flight

#### 工具链安装

```sh
sudo apt-get install android-tools-adb android-tools-fastboot fakechroot fakeroot unzip xz-utils wget python python-empy -y
```

```sh
git clone https://github.com/ATLFlight/cross_toolchain.git
```

从QDN下载Hexagon SDK 3.0：

[https://developer.qualcomm.com/download/hexagon/hexagon-sdk-v3-linux.bin](https://developer.qualcomm.com/download/hexagon/hexagon-sdk-v3-linux.bin)

你需要登录QDN才能下载，如果没有账号，可以先注册一个。

下载完成或，将下面的文件移动到`cross_toolchain/downloads`目录里面：

```sh
mv ~/Downloads/hexagon-sdk-v3-linux.bin cross_toolchain/downloads
```

安装工具链和SDK：

```sh
cd cross_toolchain
./installv3.sh
cd ..
```

按照命令的提示设置好开发环境。如果你所有选项都用了默认值，以后也可以重新执行它获取环境变量设置，此时，只会安装缺少的组件。

运行完成后，工具和SDK会安装到`$HOME/Qualcomm/...`目录中。请将下面的内容添加到`~/.bashrc`文件的末尾：

```sh
export HEXAGON_SDK_ROOT="${HOME}/Qualcomm/Hexagon_SDK/3.0"
export HEXAGON_TOOLS_ROOT="${HOME}/Qualcomm/HEXAGON_Tools/7.2.12/Tools"
export PATH="${HEXAGON_SDK_ROOT}/gcc-linaro-4.9-2014.11-x86_64_arm-linux-gnueabihf_linux/bin:$PATH"
```

重新加载配置：

```sh
source ~/.bashrc
```

#### Sysroot安装

为Snapdragon Flight做交叉编译需要用到sysroot，它可以提供必需的库和头文件。

qrlSDK sysroot位摄像头、GPU等提供了必需的头文件和库。

下载[Flight\_3.1.1\_qrlSDK.zip](http://support.intrinsyc.com/attachments/download/690/Flight_3.1.1_qrlSDK.zip)并将其保存到`cross_toolchain/download/`中。

```sh
cd cross_toolchain
unset HEXAGON_ARM_SYSROOT
./qrlinux_sysroot.sh
```

请将下面的内容添加到`~/.bashrc`文件的末尾：

```sh
export HEXAGON_ARM_SYSROOT=${HOME}/Qualcomm/qrlinux_v3.1.1_sysroot
```

重新加载配置：

```sh
source ~/.bashrc
```

请参考[Sysroot Installation](https://github.com/ATLFlight/cross_toolchain/blob/sdk3/README.md#sysroot-installation)了解更多选项。

#### 更新ADSP固件

在构建、烧写及运行之前，你需要先更新[ADSP固件](advanced-snapdragon.html#updating-the-adsp-firmware).

#### 参考资料

[ATLFlightDocs](https://github.com/ATLFlight/ATLFlightDocs/blob/master/README.md)里有很多Snapdragon Flight工具链及设置、验证的文档，它们不属于PX4开发指南。

可以用mini-dm来查看DSP发来的消息：

```sh
${HEXAGON_SDK_ROOT}/tools/debug/mini-dm/Linux_Debug/mini-dm
```

**注意**：你也可以可使用[nano-dm](https://github.com/kevinmehall/nano-dm)（尤其适用于Mac）。

### Raspberry Pi硬件

Raspberry Pi的开发者需要按照下面的指导下载RPi Linux的工具链。安装脚本会自动安装交叉编译工具链。如果你想在RPi上面做 *本地* 编译，那么请参考[这份文档](../flight_controller/raspberry_pi.md#native-builds-optional)

```sh
git clone https://github.com/pixhawk/rpi_toolchain.git
cd rpi_toolchain
./install_cross.sh
```
在安装过程中，这个脚本可能会需要你提供根用户的密码。

你可以个这个命令传递一个不同的路径，这样就会将工具链安装到你指定的目录中，默认的路径是`/opt/rpi_toolchain`。运行` ./install_cross.sh <PATH>`。安装脚本会在安装完成后自动配置所需的环境变量（你可能需要重新开启一个终端窗口才能生效）。

最后，运行下面的命令更新环境变量：

```sh
source ~/.profile
```

### Parrot Bebop

使用Parrot Bebop的开发者应该安装RPi Linux工具链。请按照[Raspberry Pi硬件](raspberry-pi-hardware)部分的指南操作。

Next, install ADB.

```sh
sudo apt-get install android-tools-adb -y
```

接下来请继续按照[构建PX4软件](../setup/building_px4.md)的指导操作。
