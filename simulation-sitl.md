# SITL（Software in the Loop）模拟

STIL模拟会在上位机上运行完整的系统以模拟自动驾驶仪的功能。它会通过本地网络拦截到模拟器。STIL的配置结构如下：

```{mermaid id:"j03cnsnq"}
graph LR;
  Simulator-->MAVLink;
  MAVLink-->SITL;
```

## 运行SITL

在确保安装了[模拟所需的前提条件](starting-installing.md)之后，只需要执行下面的make命令就可以编译并执行POSIX模拟了。

```sh
make posix_sitl_default jmavsim
```

这个命令会启动PX4 shell：

```sh
[init] shell id: 140735313310464
[init] task name: px4

______  __   __    ___
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

现在你可以准备起飞了。


pxh>
```

## 重要的文件

  * 启动脚本位于[posix-configs/SITL/init](https://github.com/PX4/Firmware/tree/master/posix-configs/SITL/init)目录，名字类似于`rcS_SIM_AIRFRAME`，默认值为`rcS_jmavsim_iris`。
  * 根文件系统（类似于`/`）位于：`build_posix_sitl_default/src/firmware/posix/rootfs/`

## 起飞

And a window with the 3D view of the [jMAVSim](http://github.com/PX4/jMAVSim.git) simulator:

![](images/sim/jmavsim.png)

在初始化完成后，这个系统会输出home position（`telem> home: 55.7533950, 37.6254270, -0.00`）。你可以输入如下命令另其起飞：

```sh
pxh> commander takeoff
```

> **信息** QGC支持摇杆及微型摇杆。 To use manual input, put the system in a manual flight mode (e.g. POSCTL, position control). Enable the thumb joystick from the QGC preferences menu.

## 模拟一个Wifi无人机

There is a special target to simulate a drone connected via Wifi on the local network:

```sh
make broadcast jmavsim
```

The simulator broadcasts his address on the local network as a real drone would do.

## 扩展和定制

To extend or customize the simulation interface, edit the files in the `Tools/jMAVSim` folder. The code can be accessed through the[jMAVSim repository](https://github.com/px4/jMAVSim) on Github.

> **信息** 构建系统 build system enforces the correct submodule to be checked out for all dependencies, including the simulator. It will not overwrite changes in files in the directory, however, when these changes are comitted the submodule needs to be registered in the Firmware repo with the new commit hash. To do so, `git add Tools/jMAVSim` and commit the change. This will update the GIT hash of the simulator.

## 同ROS的接口

这个模拟系统可以同真正的设备一样[与ROS相连接](simulation-ros-interface.md)。
