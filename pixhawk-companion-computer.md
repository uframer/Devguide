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

| TELEM2 |         | FTDI    |        |
--- | --- | ---
|1         | +5V (red)|         | DO NOT CONNECT!   |
|2         | Tx  (out)| 5       | FTDI RX (yellow) (in)   |
|3         | Rx  (in) | 4       | FTDI TX (orange) (out)  |
|4         | CTS (in) |6       | FTDI RTS (green) (out) |
|5         | RTS (out)|2       | FTDI CTS (brown) (in) |
|6         | GND     | 1       | FTDI GND (black)   |
