# UAVCAN介绍

![](images/uavcan-logo-transparent.png)

[UAVCAN](http://uavcan.org)是一种运行在嵌入式系统中的网络，自动驾驶仪可以通过它来连接各种航空电子设备。它支持的硬件包括（但不限于）：

  * 电机控制器
    * [Pixhawk ESC](https://pixhawk.org/modules/pixhawk_esc)
    * [SV2740 ESC](https://github.com/thiemar/vectorcontrol)
  * 空速传感器
    * [Thiemar airspeed sensor](https://github.com/thiemar/airspeed)
  * GPS/GLONASS的GNSS接收器
    * [Zubax GNSS](http://zubax.com/product/zubax-gnss)

In contrast to hobby-grade devices it uses rugged, differential signalling and supports firmware upgrades over the bus. All motor controllers provide status feedback and implement field-oriented-control (FOC).

## 更新节点固件

The PX4 middleware will automatically upgrade firmware on UAVCAN nodes if the matching firmware is supplied. The process and requirements are described on the [UAVCAN Firmware](uavcan-node-firmware.md) page.

## 枚举并配置电机控制器

The ID and rotational direction of each motor controller can be assigned after installation in a simple setup routine: [UAVCAN Node Enumeration](uavcan-node-enumeration.md). The routine can be started by the user through QGroundControl.

## 有用的链接

* [主页](http://uavcan.org)
* [Specification](http://uavcan.org/Specification)
* [Implementations and tutorials](http://uavcan.org/Implementations)
