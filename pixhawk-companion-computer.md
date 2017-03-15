# Pixhawk伙伴计算机

Pixhawk系列自动驾驶仪同伙伴计算机（Raspberry Pi、Odroid、Tegra K1）的接口一直采用相同的方式：它们通过`TELEM2`的串口连接，这个端口本身被设计为实现这一功能。这条链路上的通讯采用[MAVLink](http://mavlink.org)协议。

## Pixhawk设置

将`System`组里的`SYS_COMPANION`参数设置为如下值之一：

> **信息** 修改这些参数后需要重启自动驾驶仪才能生效。

  * `0` 表示禁用`TELEM2`的MAVLink输出（默认值）
  * `921600` 表示启用MAVLink输出，波特率为921600，采用8N1设置（推荐）
  * `57600` 表示启用MAVLink输出，波特率为57600 baud，采用8N1设置
  * `157600` 表示启用*OSD*模式的MAVLink输出，波特率为57600
  * `257600` 表示启用监听模式的MAVLink连接，波特率为57600

## 伙伴计算机设置

伙伴计算机需要在串口上运行能够理解MAVLink协议的软件，常见的选项有：

  * [MAVROS](ros-mavros-installation.md) 同ROS节点通信
  * [C/C++ example code](https://github.com/mavlink/c_uart_interface_example) 自己开发代码
  * [MAVProxy](http://mavproxy.org) 支持通过串口和UDP实现MAVLink通信

## 硬件设置

按照下面的指南连接串口。所有的Pixhawk串口都运行在3.3V，而且可以兼容5V电平。

> **警告** 许多现代的伙伴计算机的硬件UART只支持1.8V电平，连接到3.3V电平可能会损坏伙伴计算机。请使用电平变换器。通常，伙伴计算机的硬件串口已经被配置为其他功能（调制解调器或者控制台），你需要在*Linux里重新配置*它们的功能。

最安全的选择是使用FTDI Chip USB转串口适配器并按照如下方式连线。这种方法能用，而且最容易。

| TELEM2 |           | FTDI |                        |
| ------ | --------- | ---- | ---------------------- |
|1       | +5V (red) |      | 不要连接！              |
|2       | Tx  (out) | 5    | FTDI RX (yellow) (in)  |
|3       | Rx  (in)  | 4    | FTDI TX (orange) (out) |
|4       | CTS (in)  | 6    | FTDI RTS (green) (out) |
|5       | RTS (out) | 2    | FTDI CTS (brown) (in)  |
|6       | GND       | 1    | FTDI GND (black)       |

## Linux上的软件设置

在Linux上，USB FTDI转接器的默认设备名应该是`\dev\ttyUSB0`。如果你通过USB连接了第二个FTDI，或者连接了一个Arduino，那么它可能会是`\dev\ttyUSB1`。为了避免混淆，我们建议你创建一个指向它的符号链接，可以通过USB的vendor ID和device ID判断出哪个设备才是你的转接器。

使用`lsusb`命令可以拿到vendor ID和product ID：

```sh
$ lsusb

Bus 006 Device 002: ID 0bda:8153 Realtek Semiconductor Corp.
Bus 006 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 005 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 004 Device 002: ID 05e3:0616 Genesys Logic, Inc.
Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 004: ID 2341:0042 Arduino SA Mega 2560 R3 (CDC ACM)
Bus 003 Device 005: ID 26ac:0011
Bus 003 Device 002: ID 05e3:0610 Genesys Logic, Inc. 4-port hub
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
Bus 001 Device 002: ID 0bda:8176 Realtek Semiconductor Corp. RTL8188CUS 802.11n WLAN Adapter
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```

Arduino是`Bus 003 Device 004: ID 2341:0042 Arduino SA Mega 2560 R3 (CDC ACM)`

Pixhawk是`Bus 003 Device 005: ID 26ac:0011`

> 如果你在输出中找不到你自己的设备，可以先拔下设备，执行`lsusb`，插入设备，然后再执行`lsusb`，对比一下哪个设备是新出现的，它就是你的设备。

因此，我们可以创建一个名为`/etc/udev/rules.d/99-pixhawk.rules`的新UDEV规则，内容类似下面这样，注意把idVendor和idProduct替换成你自己的值。

```sh
SUBSYSTEM=="tty", ATTRS{idVendor}=="2341", ATTRS{idProduct}=="0042", SYMLINK+="ttyArduino"
SUBSYSTEM=="tty", ATTRS{idVendor}=="26ac", ATTRS{idProduct}=="0011", SYMLINK+="ttyPixhawk"
```

**重启** 之后，你就可以在脚本中使用`/dev/ttyPixhawk`而不是`/dev/ttyUSB0`这样的名字了。

> 请确保通过`usermod`命令将你自己添加到`tty`和`dialout`组里，不要用root执行脚本。

```sh
usermod -a -G tty ros-user
usermod -a -G dialout ros-user
```
