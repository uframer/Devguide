# PX4系统控制台

可以使用系统控制台访问系统的底层接口、调试输出或者分析系统的引导过程。连接系统控制台的最简单方法是使用[Dronecode probe](http://nicadrone.com/index.php?id_product=65&controller=product)，不过普通的FTDI线也能用。

## 系统控制台 vs. Shell

PX4有多个shell，但是只有一个控制台：系统控制台是所有引导信息（包括在引导时自动启动的应用程序的信息）输出的地方。

  * 系统控制台（第一个shell）：硬件串口
  * 其他的shell：Pixhawk on USB（也就是Mac系统里的`/dev/tty.usbmodem1`设备）

> **信息** USB shell：如果只需要运行几个命令，或者测试一个应用程序，那么连接到USB shell是很方便的。**只要你不插microSD开启动系统，就额可以启用USB shell。** 只有在调试启动阶段或者USB需要用作其他用途时（例如连接[地面站](qgroundcontrol-intro.md)），才必须要使用硬件串口控制台。

## Snapdragon Flight：控制台连线

开发者套装会附带一个breakout board，其中有三个引脚用于连接控制台。Connect the bundled FTDI cable to the header and the breakout board to the expansion connector.

## Pixracer/Pixhawk v3：控制台连线

连接JST SH 6pin 1:1线缆到Dronecode probe，或者将FTDI线缆的每个pin按下图连接：

| Pixracer / Pixhawk v3  |         | FTDI    |        |
| -- | -- | -- | -- |
|1         | +5V (red)     |         | N/C    |
|2         | UART7 Tx      | 5       | FTDI RX (yellow)  |
|3         | UART7 Rx      | 4       | FTDI TX (orange)  |
|4         | SWDIO      |         | N/C   |
|5         | SWCLK      |         | N/C   |
|6         | GND     | 1       | FTDI GND (black)   |

## Pixhawk v1：控制台连线

The system console can be accessed through the Dronecode probe or an FTDI cable. Both options are explained in the section below.

### 使用Dronecode Probe连接

Connect the 6-pos DF13 1:1 cable on the [Dronecode probe](http://nicadrone.com/index.php?id_product=65&controller=product) to the SERIAL4/5 port of Pixhawk.

![](images/console/dronecode_probe.jpg)

### 使用FTDI 3.3V线缆连接

If no Dronecode probe is at hand an FTDI 3.3V (Digi-Key: [768-1015-ND](http://www.digikey.com/product-detail/en/TTL-232R-3V3/768-1015-ND/1836393)) will do as well.

| Pixhawk 1/2  |         | FTDI    |        |
| -- | -- | -- | -- |
|1         | +5V (red)     |         | N/C    |
|2         | S4 Tx      |         | N/C   |
|3         | S4 Rx      |         | N/C   |
|4         | S5 Tx      | 5       | FTDI RX (yellow)   |
|5         | S5 Rx      | 4       | FTDI TX (orange)   |
|6         | GND     | 1       | FTDI GND (black)   |

The connector pinout is shown in the figure below.

![](images/console/console_connector.jpg)

The complete wiring is shown below.

![](images/console/console_debug.jpg)

## 打开控制台

After the console connection is wired up, use the default serial port tool of your choice or the defaults described below:

### Linux/Mac OS：Screen

Install screen on Ubuntu (Mac OS already has it installed):

<div class="host-code"></div>

```bash
sudo apt-get install screen
```

  * Serial: Pixhawk v1 / Pixracer use 57600 baud
  * Serial: Snapdragon Flight uses 115200 baud

Connect screen at BAUDRATE baud, 8 data bits, 1 stop bit to the right serial port (use `ls /dev/tty*` and watch what changes when unplugging / replugging the USB device). Common names are `/dev/ttyUSB0` and `/dev/ttyACM0` for Linux and `/dev/tty.usbserial-ABCBD` for Mac OS.

<div class="host-code"></div>

```bash
screen /dev/ttyXXX BAUDRATE 8N1
```

### Windows：PuTTY

Download [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) and start it.

Then select 'serial connection' and set the port parameters to:

  * 57600 baud
  * 8 data bits
  * 1 stop bit

## Getting Started on the Console

Type `ls` to view the local file system, type `free` to see the remaining free RAM. The console will also display the system boot log when power-cycling the board.

```bash
nsh> ls
nsh> free
```


# Snapdragon DSP Console
When you are connected to your Snapdragon board via usb you have access to the px4 shell on the posix side of things.
The interaction with the DSP side (QuRT) is enabled with the `qshell` posix app and its QuRT companion.

With the Snapdragon connected via USB, open the mini-dm to see the output of the DSP:
```
${HEXAGON_SDK_ROOT}/tools/mini-dm/Linux_Debug/mini-dm
```

Run the main app on the linaro side:
```
cd /home/linaro
./px4 px4.config
```

You can now use all apps loaded on the DSP from the linaro shell with the following syntax:
```
pxh> qshell command [args ...]
```

For example, to see the available QuRT apps:
```
pxh> qshell list_tasks
```

The output of the executed command is displayed on the minidm.
