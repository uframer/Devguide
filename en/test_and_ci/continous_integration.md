# PX4持续集成

PX4的构建和测试工作分散在多个持续集成服务之上。

## [Travis-ci](https://travis-ci.org/PX4/Firmware)

Travis-ci负责构建并测试官方的stable/beta/development二进制程序，它们可以通过[QGroundControl](http://qgroundcontrol.com/)烧写。目前它使用GCC 4.9.3并运行在docker image [px4io/px4-dev-base](https://hub.docker.com/r/px4io/px4-dev-base/)环境里，负责编译px4fmu-{v1, v2, v4}、mindpx-v2、tap-v1，makefile的目标用的是`qgc_firmware`。

Travis-ci还负责构建并测试MacOS posix sitl目标。

## [Semaphore](https://semaphoreci.com/px4/firmware)

Semaphore主要用于编译Qualcomm Snapdragon平台的修改，此外它还作为Travis-ci的备份存在，使用的是同Tranvis-ci相同的[px4io/px4-dev-base](https://hub.docker.com/r/px4io/px4-dev-base/) docker image。除了编译Travis-ci会编译的固件外，Semaphore还会构建stm32discovery和crazyflie，而且会运行单元测试并验证编码风格。

## [CircleCI](https://circleci.com/gh/PX4/Firmware)

CircleCI会使用下一个可能会用于stable固件的GCC版本来构建，它使用[px4io/px4-dev-nuttx-gcc_next](https://hub.docker.com/r/px4io/px4-dev-nuttx-gcc_next/)这个docker image。使用的makefile目标是`quick_check`，它会编译px4fmu-v4_default、posix_sitl_default，运行测试并验证编码风格。
