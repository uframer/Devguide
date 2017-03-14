# 构建PX4软件

PX4可以在控制台或者图形开发环境/IDE中构建。

## 在控制台里编译

在你使用图形编辑器或者IDE之前，最好先验证一下系统的设置。请在OS X上启动终端程序。如果你用Ubuntu，请点击启动栏并搜索'terminal'。在Windows上，请在开始菜单里找到PX4目录并点击里面的'PX4 Console'。

![](images/toolchain/terminal.png)

终端通常会启动在用户的主目录。我们假设你会将上游代码克隆到`~/src/Firmware`目录。有经验的开发者可以克隆[自己的分支](https://help.github.com/articles/fork-a-repo/)。

```sh
mkdir -p ~/src
cd ~/src
git clone https://github.com/PX4/Firmware.git
cd Firmware
git submodule update --init --recursive
cd ..
```
现在可以从源代码编译二进制文件了。但是在下载到硬件之前，我们推荐你先用软件[模拟](simulation-sitl.md)一下。喜欢使用图形开发环境的用户可以继续阅读下一节。

### 基于NuttX/Pixhawk平台


```sh
cd Firmware
make px4fmu-v2_default
```

请注意这里的语法：'make'是构建工具的名字，'px4fmu-v2'硬件/自动驾驶仪的版本，'default'是指采用默认的配置。所有的PX4构建目标都遵循这样的格氏。如果构建成功，那么会在控制台上看到这样的输出：

```sh
[100%] Linking CXX executable firmware_nuttx
[100%] Built target firmware_nuttx
Scanning dependencies of target build_firmware_px4fmu-v2
[100%] Generating nuttx-px4fmu-v2-default.px4
[100%] Built target build_firmware_px4fmu-v2
```

如果你在上面的make命令后加上'upload'，那么会将编译出的二进制文件通过USB上传到自动驾驶仪上：

```sh
make px4fmu-v2_default upload
```

如果运行成功，那么会在控制台上看到如下输出：

```sh
Erase  : [====================] 100.0%
Program: [====================] 100.0%
Verify : [====================] 100.0%
Rebooting.

[100%] Built target upload
```
### Raspberry Pi 2/3开发板

下面的命令会为Raspbian生成二进制文件。

#### 交叉构建

```sh
cd Firmware
make posix_rpi_cross # 这个target表示交叉构建
```

构建完成后，会在`build_posix_rpi_cross/src/firmware/posix`目录下生成名为`px4`的可执行文件。请确保你通过ssh连到了RPi，具体操作请参考[如何访问你的RPi](hardware-rpi.md#developer-quick-start)。

请使用如下命令设置RPi使用的IP(或者主机名)：

```sh
export AUTOPILOT_HOST=192.168.X.X
```

使用如下命令上传可执行文件到RPi：

```sh
cd Firmware
make posix_rpi_cross upload # 针对交叉构建
```

然后，登录ssh并以root身份运行：

```sh
sudo ./px4 px4.config
```

#### 原生构建

如果你打算*直接*在RPi上构建，请使用`posix_rpi_native`这个target构建：

```sh
cd Firmware
make posix_rpi_native # 做原生构建
```

构建完成后，会在`build_posix_rpi_native/src/firmware/posix`目录下生成名为`px4`的可执行文件。可以在RPi上直接通过如下命令运行它：

```sh
sudo ./build_posix_rpi_native/src/firmware/posix/px4 ./posix-configs/rpi/px4.config
```

执行px4会产生如下输出：

```sh

______  __   __    ___
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

px4 starting.

pxh>
```

#### Autostart
如果想要自动启动px4，请将下面命令添加到`/etc/rc.local`文件中，注意要放在`exit 0`这行之前：
```
cd /home/pi && ./px4 -d px4.config > px4.log
```


### Parrot Bebop

我们对Bebop处在相当初期的阶段，使用时请加倍小心。

```sh
cd Firmware
make posix_bebop_default
```

打开你的Bebop并将你的上位机连接到Bebop的Wi-Fi。然后，按电源键四次以启动ADB和telnet守护进程。

```sh
make posix_bebop_default upload
```

这个命令会将PX4 mainapp上传到`/usr/bin`并创建`/home/root/parameters`。此外，我们还需要Bebop的混控器文件和`px4.config`。目前，这两个文件都需要手动复制：

```sh
adb connect 192.168.42.1:9050
adb push ROMFS/px4fmu_common/mixers/bebop.main.mix /home/root
adb push posix-configs/bebop/px4.config /home/root
adb disconnect
```

#### 运行！

连接到Bebop的Wi-Fi并按下电源键四次。接下来，通过telnet或者adb shell连接Bebop，然后执行后面的命令。

```sh
telnet 192.168.42.1
```

用下面的命令杀掉Bebop的专有驱动：

```sh
kk
```
and start the PX4 mainapp with:
```sh
px4 /home/root/px4.config
```

如果想让Bebop飞起来，将摇杆连入上位机，然后启动QGroundControl。确保Bebop和摇杆被上位机正确地识别，按照QGC的指令校准传感器并设置摇杆。

### 基于QuRT/Snapdragon的平台

#### 构建

下面的命令会分别构建Linux和DSP的文件。这两个可执行文件通过[muORB协议](advanced-uorb.md)通信。

```sh
cd Firmware
make eagle_default
```

如果要将这些可执行文件加载到硬件上，请用USB线连接Snapdragon，等待设备启动完成。在终端执行如下命令：

```sh
adb shell
```

回到前面编译固件用的终端并运行如下命令上传固件：

```sh
make eagle_default upload
```

请注意，这个命令还会复制（并覆盖）两个配置文件：[mainapp.config](https://github.com/PX4/Firmware/blob/master/posix-configs/eagle/flight/mainapp.config) and [px4.config](https://github.com/PX4/Firmware/blob/master/posix-configs/eagle/flight/px4.config)。这两个文件会被分别存储在`/usr/share/data/adsp/px4.config`和`/home/linaro/mainapp.config`，如果想，请直接在设备上编辑这两个文件。

目前你需要手动复制混控器：

```sh
adb push ROMFS/px4fmu_common/mixers/quad_x.main.mix  /usr/share/data/adsp
```

#### 运行

运行DSP debug monitor:

```sh
${HEXAGON_SDK_ROOT}/tools/debug/mini-dm/Linux_Debug/mini-dm
```

**注意**：你也可以使用（尤其适用于Mac）[nano-dm](https://github.com/kevinmehall/nano-dm)。

回到ADB shell然后运行px4：

```sh
cd /home/linaro
./px4 mainapp.config
```

请注意，px4会在你断开USB电缆（或者你的ssh会话断开）后立即停止运行。如果你要飞行，你应该使px4在启动后自动运行。

#### 自动启动

如果想在Snapdragon启动时运行px4，请将启动px4的命令添加到`rc.local`：

第一种方式是直接边界Snapdragon的`/etc/rc.local`文件：

```sh
adb shell
vim /etc/rc.local
```

或者复制这个文件到你的计算机，编辑它，然后再复制回去：

```sh
adb pull /etc/rc.local
gedit rc.local
adb push rc.local /etc/rc.local
```

如果要自动启动，请在`exit 0`前加入下面的内容：

```sh
(cd /home/linaro && ./px4 mainapp.config > mainapp.log)

exit 0
```

请确保`rc.local`具有执行权限：

```sh
adb shell
chmod +x /etc/rc.local
```

然后重启Snapdragon：

```sh
adb reboot
```

## 在图形化IDE

PX4系统支持Qt Creator、Eclipse和Sublime Text。在这里几个选择里面，Qt Creator是对用户最友好的，因此它也是我们唯一提供官方支持的IDE。如果你不是Eclipse活着Sublime的专家，请不要使用它们。如果你坚持使用它们，可以打开源码树中的[Eclipse项目文件](https://github.com/PX4/Firmware/blob/master/.project)或者[Sublime项目文件](https://github.com/PX4/Firmware/blob/master/Firmware.sublime-project)。

{% youtube %}https://www.youtube.com/watch?v=Bkk8zttWxEI&rel=0&vq=hd720{% endyoutube %}

## Qt Creator功能

Qt creator提供了符号跳转、自动补全以及构建功能。

![](images/toolchain/qtcreator.png)

### 在Linux上运行Qt Creator

在启动Qt Creator之前，需要先生成[项目文件](https://cmake.org/Wiki/CMake_Generator_Specific_Information#Code::Blocks_Generator)：

```sh
cd ~/src/Firmware
mkdir ../Firmware-build
cd ../Firmware-build
cmake ../Firmware -G "CodeBlocks - Unix Makefiles"
```

然后，通过`File`->`Open File or Project`菜单选择打开`Firmware`目录下的`CMakeLists.txt`文件。

打开后，可以通过选择`custom executable`来配置`play`按钮，然后将`make`设置为可执行文件，将`upload`设置为参数。

### 在Windows上运行Qt Creator

> ** 我们并没有在Windows上验证过Qt creator。 **

### 在Mac OS上运行Qt Creator

在运行Qt Creator之前，需要先生成[项目文件](https://cmake.org/Wiki/CMake_Generator_Specific_Information#Code::Blocks_Generator)：

```sh
cd ~/src/Firmware
mkdir build_creator
cd build_creator
cmake .. -G "CodeBlocks - Unix Makefiles"
```

搞定！启动Qt Creator，然后安装下面视频的指导构建。

{% youtube %}https://www.youtube.com/watch?v=0pa0gS30zNw&rel=0&vq=hd720{% endyoutube %}
