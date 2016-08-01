# Pixracer

Pixhawk XRacer控制板家族为小型竞速四轴和固定翼飞行器做了优化。同[Pixfalcon](hardware-pixfalcon.md)和[Pixhawk](hardware-pixhawk.md)相比，它集成了Wifi，使用新的传感器、全尺寸伺服器接口，提供CAN总线并支持2M闪存。

![](images/hardware/hardware-pixracer.jpg)

## 简介

<aside class="tip">
主要的硬件文档在[pixhawk.org](https://pixhawk.org/modules/pixracer)：
</aside>

  * 主SoC：[STM32F427VIT6 rev.3](http://www.st.com/web/en/catalog/mmc/FM141/SC1169/SS1577/LN1789)
    * CPU: 180 MHz ARM Cortex M4 with single-precision FPU
    * RAM: 256 KB SRAM (L1)
  * 采用标准的FPV尺寸：36x36mm，标准30.5mm孔距
  * 通过Wifi实现电传和软件升级
  * Invensense ICM-20608加速度计/陀螺仪（4 KHz）
  * MPU9250加速度计/陀螺仪/磁力计（4 KHz）
  * HMC5983带有温度补偿的磁力计
  * Measurement Specialties MS5611气压计
  * JST GH连接器
  * microSD（用于记录日志）
  * S.BUS/Spektrum/SUMD/PPM输入
  * FrSky数传接口
  * OneShot PWM输出（可配置）
  * 可选的安全开关和蜂鸣器
  * 购买链接：
    * [AUAV Pixracer](http://www.auav.co/product-p/xr-v1.htm)
  * 附件：
    * [Digital空速管](http://www.hobbyking.com/hobbyking/store/__62752__HKPilot_32_Digital_Air_Speed_Sensor_And_Pitot_Tube_Set.html)
    * [Hobbyking OSD + US Telemetry (915 MHz)](http://www.hobbyking.com/hobbyking/store/__74651__Micro_HKPilot_Telemetry_Radio_Module_with_On_Screen_Display_OSD_unit_915MHz_.html)
    * [Hobbyking OSD + EU Telemetry (433 MHz)](http://www.hobbyking.com/hobbyking/store/__74650__Micro_HKPilot_Telemetry_Radio_Module_with_On_Screen_Display_OSD_unit_433MHz_.html)


## 器件包

Pixracer需要独立的供电，这样可以避免从电机或者ESC来的电涌倒灌到飞行控制器以至于干扰奥传感器。

  * 电源模块（带有电压计和电流计）
  * I2C分线器（支持AUAV、Hobbyking和3DR的外围设备）
  * 常见外围设备的线缆

## Wifi（无需USB）

Pixracer的一个主要特点是它能够通过Wifi来烧写新固件、设置系统参数并可以作为飞行时的数传接口。有了它，你就可以完全脱离桌面计算机了。

<aside class="todo">
现在已经支持系统设置和数传功能，固件更新功能已经在引导程序里支持，但是没有默认打开。
</aside>
<p/>
  * [ESP8266文档和固件刷写指南](https://pixhawk.org/peripherals/8266)
  * [自定义ESP8266 MAVLink固件](https://github.com/dogmaphobic/mavesp8266)
