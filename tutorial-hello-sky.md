# 开发第一个App：Hello Sky

这个教程演示了如何编写并运行一个PX4系统应用程序。

## 前提条件

  * 同Pixhawk或者Snapdragon兼容的自动驾驶仪
  * [安装了PX4工具链](starting-installing.md)
  * Github账户（[免费注册](https://github.com/signup/free)）

## 第1步：准备文件

为了方便你管理新添加的代码，避免影响后续从主代码库pull变更，我们建议你用fork一个Firmware代码库的分支出来：

  - [注册](https://github.com/signup/free)Github
  - 去[Firmware的代码仓库网页](https://github.com/px4/Firmware/)并点击右上角的 **FORK** 。
  - 打开新fork出的项目的网页，复制出这个新分支的URL。
  - 克隆这个新分支到本地，例如，使用`git clone https://github.com/<youraccountname>/Firmware.git`命令。Windows用户请参考[Github网站的帮助信息](https://help.github.com/articles/set-up-git#platform-windows)并使用Github for Windows客户端做fork/clone。（🐷：Mac也可以用桌面客户端做）
  - 更新git子模块：在你的shell（Windows用户请用PX4控制台）输入如下命令：

<div class="host-code"></div>

```sh
cd Firmware
git submodule init
git submodule update --recursive
```

你可以进入`Firmware/src/examples/`目录看看这些示例的样子。

## Step 2: 最小的应用程序

在`px4_simple_app`目录下新建一个名为`px4_simple_app.c`的文件。这个目录现在已经在了，为了达到更好的学习效果，请将已有的目录删除。

编辑这个文件，像下面那样添加默认的头文件和`main`函数。

<aside class="tip">
请注意这个文件中的代码风格 —— 如果你要为PX4贡献代码，请一定遵循这种风格。
</aside>

```c
/****************************************************************************
 *
 *   Copyright (c) 2012-2016 PX4 Development Team. All rights reserved.
 *
 * Redistribution and use in source and binary forms, with or without
 * modification, are permitted provided that the following conditions
 * are met:
 *
 * 1. Redistributions of source code must retain the above copyright
 *    notice, this list of conditions and the following disclaimer.
 * 2. Redistributions in binary form must reproduce the above copyright
 *    notice, this list of conditions and the following disclaimer in
 *    the documentation and/or other materials provided with the
 *    distribution.
 * 3. Neither the name PX4 nor the names of its contributors may be
 *    used to endorse or promote products derived from this software
 *    without specific prior written permission.
 *
 * THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
 * "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
 * LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
 * FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE
 * COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
 * INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING,
 * BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS
 * OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED
 * AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
 * LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN
 * ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
 * POSSIBILITY OF SUCH DAMAGE.
 *
 ****************************************************************************/

/**
 * @file px4_simple_app.c
 * Minimal application example for PX4 autopilot
 *
 * @author Example User <mail@example.com>
 */

#include <px4_config.h>
#include <px4_tasks.h>
#include <px4_posix.h>
#include <unistd.h>
#include <stdio.h>
#include <poll.h>
#include <string.h>

#include <uORB/uORB.h>
#include <uORB/topics/sensor_combined.h>
#include <uORB/topics/vehicle_attitude.h>

__EXPORT int px4_simple_app_main(int argc, char *argv[]);

int px4_simple_app_main(int argc, char *argv[])
{
	PX4_INFO("Hello Sky!");
	return OK;
}
```

## 第3步：在NuttShell中注册应用程序并构建

现在这个同应用程序已经完成并且可以运行，但是我们还没有将它注册为一个NuttShell命令。To enable the compilation of the application into the firmware, add it to the list of modules to build, which is here:

  * Posix SITL: [Firmware/cmake/configs/posix_sitl_default.cmake](https://github.com/PX4/Firmware/blob/master/cmake/configs/posix_sitl_default.cmake)
  * Pixhawk v1/2: [Firmware/cmake/configs/nuttx_px4fmu-v2_default.cmake](https://github.com/PX4/Firmware/blob/master/cmake/configs/nuttx_px4fmu-v2_default.cmake)
  * Pixracer: [Firmware/cmake/configs/nuttx_px4fmu-v4_default.cmake](https://github.com/PX4/Firmware/blob/master/cmake/configs/nuttx_px4fmu-v4_default.cmake)

Create a new line for your application somewhere in the file:

  `examples/px4_simple_app`

Build it:

  * Pixhawk v1/2: `make px4fmu-v2_default`
  * Pixhawk v3: `make px4fmu-v4_default`

## Step 4: Upload and Test the app

Enable the uploader and then reset the board:

  * Pixhawk v1/2: `make px4fmu-v2_default upload`
  * Pixhawk v3: `make px4fmu-v4_default upload`

It should print before you reset the board a number of compile messages and at the end:

  `Loaded firmware for X,X, waiting for the bootloader...`

Once the board is reset, and uploads, it prints:

<div class="host-code"></div>

```sh
Erase  : [====================] 100.0%
Program: [====================] 100.0%
Verify : [====================] 100.0%
Rebooting.

[100%] Built target upload
```

### 连接到控制台

现在，通过串口或者USB连接到[系统控制台](advanced-system-console.md)。按下回车键后会显示shell提示符：

```sh
  nsh>
```


输入`help`然后按回车：

```sh
  nsh> help
    help usage:  help [-v] [<cmd>]

    [           df          kill        mkfifo      ps          sleep
    ?           echo        losetup     mkrd        pwd         test
    cat         exec        ls          mh          rm          umount
    cd          exit        mb          mount       rmdir       unset
    cp          free        mkdir       mv          set         usleep
    dd          help        mkfatfs     mw          sh          xd

  Builtin Apps:
    reboot
    perf
    top
    ..
    px4_simple_app
    ..
    sercon
    serdis
```

注意，现在`px4_simple_app`已经成为shell中一个可用的命令了。Start it by typing `px4_simple_app` and ENTER:

```sh
  nsh> px4_simple_app
  Hello Sky!
```

The application is now correctly registered with the system and can be extended to actually perform useful tasks.

## Step 5: Subscribing Sensor Data

To do something useful, the application needs to subscribe inputs and publish outputs (e.g. motor or servo commands). Note that the *true* hardware abstraction of the PX4 platform comes into play here -- no need to interact in any way with sensor drivers and no need to update your app if the board or sensors are updated.

Individual message channels between applications are called *topics* in PX4. For this tutorial, we are interested in the [sensor_combined](https://github.com/PX4/Firmware/blob/master/src/modules/uORB/topics/sensor_combined.h) [topic](advanced-uorb.md), which holds the synchronized sensor data of the complete system.

Subscribing to a topic is swift and clean:

```C++
#include <uORB/topics/sensor_combined.h>
..
int sensor_sub_fd = orb_subscribe(ORB_ID(sensor_combined));
```

The `sensor_sub_fd` is a topic handle and can be used to very efficiently perform a blocking wait for new data. The current thread goes to sleep and is woken up automatically by the scheduler once new data is available, not consuming any CPU cycles while waiting. To do this, we use the [poll()](http://pubs.opengroup.org/onlinepubs/007908799/xsh/poll.html) POSIX system call.

Adding `poll()` to the subscription looks like (*pseudocode, look for the full implementation below*):

```C++
#include <poll.h>
#include <uORB/topics/sensor_combined.h>
..
int sensor_sub_fd = orb_subscribe(ORB_ID(sensor_combined));

/* one could wait for multiple topics with this technique, just using one here */
px4_pollfd_struct_t fds[] = {
	{ .fd = sensor_sub_fd,   .events = POLLIN },
};

while (true) {
	/* wait for sensor update of 1 file descriptor for 1000 ms (1 second) */
	int poll_ret = px4_poll(fds, 1, 1000);
..
	if (fds[0].revents & POLLIN) {
		/* obtained data for the first file descriptor */
		struct sensor_combined_s raw;
		/* copy sensors raw data into local buffer */
		orb_copy(ORB_ID(sensor_combined), sensor_sub_fd, &raw);
		PX4_INFO("Accelerometer:\t%8.4f\t%8.4f\t%8.4f",
					(double)raw.accelerometer_m_s2[0],
					(double)raw.accelerometer_m_s2[1],
					(double)raw.accelerometer_m_s2[2]);
	}
}
```

Compile the app now by issuing

<div class="host-code"></div>

```
  make
```

### Testing the uORB Subscription

The final step is to start your application as background application.

```
  px4_simple_app &
```

Your app will flood the console with the current sensor values:

```
  [px4_simple_app] Accelerometer:   0.0483          0.0821          0.0332
  [px4_simple_app] Accelerometer:   0.0486          0.0820          0.0336
  [px4_simple_app] Accelerometer:   0.0487          0.0819          0.0327
  [px4_simple_app] Accelerometer:   0.0482          0.0818          0.0323
  [px4_simple_app] Accelerometer:   0.0482          0.0827          0.0331
  [px4_simple_app] Accelerometer:   0.0489          0.0804          0.0328
```

It will exit after printing five values. The next tutorial page will explain how to write a deamon which can be controlled from the commandline.

## Step 7: Publishing Data

To use the calculated outputs, the next step is to *publish* the results. If we use a topic from which we know that the ''mavlink'' app forwards it to the ground control station, we can even look at the results. Let's hijack the attitude topic for this purpose.

The interface is pretty simple: Initialize the struct of the topic to be published and advertise the topic:

```C
#include <uORB/topics/vehicle_attitude.h>
..
/* advertise attitude topic */
struct vehicle_attitude_s att;
memset(&att, 0, sizeof(att));
orb_advert_t att_pub_fd = orb_advertise(ORB_ID(vehicle_attitude), &att);
```

In the main loop, publish the information whenever its ready:

```C
orb_publish(ORB_ID(vehicle_attitude), att_pub_fd, &att);
```

The modified complete example code is now:

```C
#include <px4_config.h>
#include <px4_tasks.h>
#include <px4_posix.h>
#include <unistd.h>
#include <stdio.h>
#include <poll.h>
#include <string.h>

#include <uORB/uORB.h>
#include <uORB/topics/sensor_combined.h>
#include <uORB/topics/vehicle_attitude.h>

__EXPORT int px4_simple_app_main(int argc, char *argv[]);

int px4_simple_app_main(int argc, char *argv[])
{
	PX4_INFO("Hello Sky!");

	/* subscribe to sensor_combined topic */
	int sensor_sub_fd = orb_subscribe(ORB_ID(sensor_combined));
	/* limit the update rate to 5 Hz */
	orb_set_interval(sensor_sub_fd, 200);

	/* advertise attitude topic */
	struct vehicle_attitude_s att;
	memset(&att, 0, sizeof(att));
	orb_advert_t att_pub = orb_advertise(ORB_ID(vehicle_attitude), &att);

	/* one could wait for multiple topics with this technique, just using one here */
	px4_pollfd_struct_t fds[] = {
		{ .fd = sensor_sub_fd,   .events = POLLIN },
		/* there could be more file descriptors here, in the form like:
		 * { .fd = other_sub_fd,   .events = POLLIN },
		 */
	};

	int error_counter = 0;

	for (int i = 0; i < 5; i++) {
		/* wait for sensor update of 1 file descriptor for 1000 ms (1 second) */
		int poll_ret = px4_poll(fds, 1, 1000);

		/* handle the poll result */
		if (poll_ret == 0) {
			/* this means none of our providers is giving us data */
			PX4_ERR("Got no data within a second");

		} else if (poll_ret < 0) {
			/* this is seriously bad - should be an emergency */
			if (error_counter < 10 || error_counter % 50 == 0) {
				/* use a counter to prevent flooding (and slowing us down) */
				PX4_ERR("ERROR return value from poll(): %d", poll_ret);
			}

			error_counter++;

		} else {

			if (fds[0].revents & POLLIN) {
				/* obtained data for the first file descriptor */
				struct sensor_combined_s raw;
				/* copy sensors raw data into local buffer */
				orb_copy(ORB_ID(sensor_combined), sensor_sub_fd, &raw);
				PX4_INFO("Accelerometer:\t%8.4f\t%8.4f\t%8.4f",
					 (double)raw.accelerometer_m_s2[0],
					 (double)raw.accelerometer_m_s2[1],
					 (double)raw.accelerometer_m_s2[2]);

				/* set att and publish this information for other apps */
				att.roll = raw.accelerometer_m_s2[0];
				att.pitch = raw.accelerometer_m_s2[1];
				att.yaw = raw.accelerometer_m_s2[2];
				orb_publish(ORB_ID(vehicle_attitude), att_pub, &att);
			}

			/* there could be more file descriptors here, in the form like:
			 * if (fds[1..n].revents & POLLIN) {}
			 */
		}
	}

	PX4_INFO("exiting");

	return 0;
}
```

## Running the final example

And finally run your app:

```sh
  px4_simple_app
```

If you start QGroundControl, you can check the sensor values in the real time plot (Tools -> Analyze)

## Wrap-Up

This tutorial covered everything needed to develop a "grown up" PX4 autopilot application. Keep in mind that the full list of uORB messages / topics is [available here](https://github.com/PX4/Firmware/tree/master/msg/) and that the headers are well documented and serve as reference.
