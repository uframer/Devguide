# Windows开发环境

> **警告** 尽管我们提供了Windows工具链，但是我们并不提供官方支持，而且我们不推荐你使用它。它的编译速度很慢，而且并不支持新的目标板（例如Snapdragon Flight）。而且，你也不能运行很多卡发着使用的标准机器人软件包，例如用于计算机视觉和导航的软件包。在你决定使用Windows作为开发平台之前，请先考虑使用[Ubuntu](http://ubuntu.com)的双启动系统。

## 开发环境安装

下载并安装如下软件：

  * [Qt Creator IDE](http://www.qt.io/download-open-source/#section-6)
  * [PX4 Toolchain Installer v14 for Windows Download](http://firmware.diydrones.com/Tools/PX4-tools/px4_toolchain_installer_v14_win.exe)（32/64位系统，完整的构建系统，驱动程序）
  * [PX4 USB驱动程序](http://pixhawk.org/static/px4driver.msi) (32/64位系统)

接下来请按照[构建PX4软件](starting-building.md)的指导操作。

## 新方法！Bash on Windows

现在Windows用户有一个新的选项了，你基本上可以套用Linux的指令。参见[BashOnWindows](https://github.com/Microsoft/BashOnWindows)。我们已经验证过，PX4可以在这个环境中成功地构建。现在我们还不能在这个环境中烧写固件，但是你可以使用Windows上的Mission Planner或者QGroundControl来烧写自定义固件。
