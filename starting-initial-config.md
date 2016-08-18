# 初始配置

在动手开发PX4前，你应该先用默认配置设置好系统，以便验证硬件功能正常。下面的视频用[Pixhawk自动驾驶仪](hardware-pixhawk.md)和[QGroundControl](qgroundcontrol-intro.md)地面站解释了设置的过程。我们在[这里](airframes-architecture.md)列出了支持的机架。

> **信息** [下载QGroundControl](http://qgroundcontrol.org/downloads)然后按照下面视频的指导操作配置你的自动驾驶仪。请参考[QGroundControl教程](http://dev.px4.io/qgroundcontrol-intro.html)中对任务规划、飞行和参数设置细节的介绍。

下面的视频介绍了一些设置选项。

<iframe width="560" height="315" src="https://www.youtube.com/embed/91VGmdSlbo4" frameborder="0" allowfullscreen></iframe>

## 无线控制选项

PX4并不强制使用无线控制系统。它还不强制要求为每个飞行模式制定一个独立的遥控开关。

### 脱离无线控制飞行

所有对无线控制设置的检查都可以通过将`COM_RC_IN_MODE`设为`1`禁用。这样就不允许手动飞行了。

### 单通道模式切换

除了使用多个开关切换通道，还可以使用单一通道来切换飞行模式。具体的细节请参考[旧版wiki](https://pixhawk.org/peripherals/radio-control/opentx/single_channel_mode_switch).
