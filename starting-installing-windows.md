# Windows开发环境

> **警告** Although a Windows toolchain is available, its not officially supported and we discourage its use. It is unbearably slow during Firmware compilation and does not support new boards like Snapdragon Flight. It also cannot run the standard robotics software packages many developers use to prototype computer vision and navigation. Before starting to develop on Windows, consider installing a dual-boot environment with [Ubuntu](http://ubuntu.com).

## 开发环境安装

下载并安装如下软件：

  * [Qt Creator IDE](http://www.qt.io/download-open-source/#section-6)
  * [PX4 Toolchain Installer v14 for Windows Download](http://firmware.diydrones.com/Tools/PX4-tools/px4_toolchain_installer_v14_win.exe) (32/64 bit systems, complete build system, drivers)
  * [PX4 USB驱动程序](http://pixhawk.org/static/px4driver.msi) (32/64位系统)

接下来请按照[构建PX4软件](starting-building.md)的指导操作。

## 新方法！Bash on Windows

现在Windows用户有一个新的选项了，你基本上可以套用Linux的指令。参见[BashOnWindows](https://github.com/Microsoft/BashOnWindows)。我们已经验证过，PX4可以在这个环境中成功地构建。现在我们还不能在这个环境中烧写固件，但是你可以使用Windows上的Mission Planner或者QGroundControl来烧写自定义固件。