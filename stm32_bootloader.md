# STM32引导程序

PX4引导程序的代码保存在[Github](https://github.com/px4/bootloader)上。

## 支持的平台

  * FMUv1 (PX4FMU, STM32F4)
  * FMUv2 (Pixhawk 1, STM32F4)
  * FMUv3 (Pixhawk 2, STM32F4)
  * FMUv4 (Pixracer 3 and Pixhawk 3 Pro, STM32F4)
  * FMUv5 (Pixhawk 4, STM32F7)
  * TAPv1 (TBA, STM32F4)
  * ASCv1 (TBA, STM32F4)

## 构建引导程序

```bash
git clone https://github.com/PX4/Bootloader.git
cd Bootloader
make
```

构建完成后，会为所有支持的平台生成对应的elf文件。

## 烧写

> **重要**：对于某些平台来说，一定要使用正确的上电顺序才能保证可以访问JTAG/SWD。请严格地遵循下面的步骤。The instructions below are valid for a Blackmagic / Dronecode probe. Other JTAG probes will need different but similar steps. Developers attempting to flash the bootloader should have the required knowledge. If you do not know how to do this you probably should reconsider if you really need to change anything about the bootloader.

  * 断开JTAG线
  * 连接USB电源线
  * 连接JTAG线

### Black Magic / Dronecode Probe

#### 使用正确的串口

  * LINUX：```/dev/serial/by-id/usb-Black_Sphere_XXX-if00```
  * MAC OS：请使用名为`cu.xxx`的串口，不要使用`tty.xxx`这些，```tar ext /dev/tty.usbmodemDDEasdf```

```bash
arm-none-eabi-gdb
  (gdb) tar ext /dev/serial/by-id/usb-Black_Sphere_XXX-if00
  (gdb) mon swdp_scan
  (gdb) attach 1
  (gdb) mon option erase
  (gdb) mon erase_mass
  (gdb) load tapv1_bl.elf
        ...
        Transfer rate: 17 KB/sec, 828 bytes/write.
  (gdb) kill
```

### J-Link

下面的指令适用于[J-Link GDB server](https://www.segger.com/jlink-gdb-server.html)。

#### 前提条件

[Download the J-Link software](https://www.segger.com/downloads/jlink#) from the Segger website and install it according to their instructions.

#### 运行JLink GDB server

FMUv1:
```bash
JLinkGDBServer -select USB=0 -device STM32F405RG -if SWD-DP -speed 20000
```

AeroFC:
```bash
JLinkGDBServer -select USB=0 -device STM32F429AI -if SWD-DP -speed 20000
```

#### 连接GDB

```bash
arm-none-eabi-gdb
  (gdb) tar ext :2331
  (gdb) load aerofcv1_bl.elf
```

### 常见问题

If any of the commands above are not found, you are either not using a Blackmagic probe or its software is outdated. Upgrade the on-probe software first.

If this error message occurs:
```Error erasing flash with vFlashErase packet```

Disconnect the target (while leaving JTAG connected) and run

```bash
mon tpwr disable
swdp_scan
attach 1
load tapv1_bl.elf
```

This will disable target powering and attempt another flash cycle.
