# Lightware SF1XX光学雷达设置
----------------------------------------------------

This page shows you how to set up one of following lidars:
 1. SF10/a
 2. SF10/b
 3. SF10/c
 4. SF11/c

驱动程序现在只支持i2c连接。
![](images/hardware/sf1xx_i2c.jpg)

## 配置光学雷达
--------------------------------------------------------

You should connect to sensor via usb (it has internal usb to serial converter), run terminal, press `space` and check that i2c address equal to `0x66`.
Newer sensor versions already have `0x66` preconfigured. Older have `0x55` which conflicts with `rgbled` module.


## 配置PX4
--------------------------------------------------------

Use the `SENS_EN_SF1XX` parameter to select the lidar model and then reboot.
* `0` lidar disabled
* `1` SF10/a
* `2` SF10/b
* `3` SF10/c
* `4` SF11/c
