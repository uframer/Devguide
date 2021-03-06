# 目录

* [介绍](README.md)✅
* 上手
    * [初始配置](starting-initial-config.md)✅
    * [安装工具链](starting-installing.md)✅
        * [Mac OS](starting-installing-mac.md)✅
        * [Linux](starting-installing-linux.md)✅
            * [Arch及CentOS发行版指南](starting-installing-linux-boutique.md)✅
        * [Windows](starting-installing-windows.md)✅
    * [构建代码](starting-building.md)✅
    * [贡献&征集开发者](starting-contributing.md)
        * [GIT示例](git-examples.md)
* 概念
    * [飞行模式/操作](concept-flight-modes.md)✅
    * [架构概览](concept-architecture.md)✅
    * [飞行软件栈](concept-flight-stack.md)✅
    * [中间件](concept-middleware.md)✅
    * [混控和执行机构](concept-mixing.md)⏳
    * [PWM limit状态机](concept-pwm_limit.md)⏳
* [教程](tutorials.md)✅
    * [地面站](qgroundcontrol-intro.md)⏳
    * [如何编写一个应用程序](tutorial-hello-sky.md)⏳
    * [QGC的视频流功能](advanced-videostreaming-qgc.md)
    * [远程视频流](wifibroadcast.md)
  * [光流和激光雷达](flow_lidar_setup.md)
    * [集成测试](tutorial-integration-testing.md)
    * [室外光流](optical-flow-outdoors.md)
    * [ecl EKF](tuning_the_ecl_ekf.md)
    * [飞行前检查](pre_flight_checks.md)
    * [数传](telemetry.md)✅
    * [传感器的热补偿](sensor-thermal-calibration.md)
    * [地面检测器](land-detector.md)
* 模拟
    * [基本模拟](simulation-sitl.md)
    * [Gazebo模拟](simulation-gazebo.md)
    * [HITL模拟](simulation-hitl.md)
    * [连接到ROS](simulation-ros-interface.md)
* 自动驾驶仪硬件
    * [Crazyflie 2.0](hardware-crazyflie2.md)
    * [Intel Aero](hardware-intel-aero.md)
    * [Pixfalcon](hardware-pixfalcon.md)
    * [Pixhawk](hardware-pixhawk.md)
    * [Pixracer](hardware-pixracer.md)
    * [Raspberry Pi](hardware-rpi.md)
    * [Snapdragon Flight](hardware-snapdragon.md)
        * [光流](optical_flow.md)
        * [Snapdragon高级话题](advanced-snapdragon.md)
            * [如何访问I/O数据](advanced-accessing-io-data.md)
            * [摄像头和光流](advanced-snapdragon_camera.md)
* 中间件和架构
    * [uORB消息机制](advanced-uorb.md)
    * [自定义MAVLink消息](custom-mavlink-message.md)
    * [守护进程](architecture-daemon.md)
    * [驱动框架](advanced-drivers.md)
* 机架
    * [统一的代码库](airframes-architecture.md)✅
    * [如何添加一个新的机架](airframes-adding-a-new-frame.md)⏳
    * [多轴飞行器](airframes-multicopter.md)✅
        * [电机编号图](airframes-motor-map.md)✅
        * [QAV 250 Racer](airframes-multicopter-qav250.md)✅
        * [Matrice 100](airframes-multicopter-matrice100.md)✅
        * [QAV-R](qav-r.md)✅
    * [固定翼飞行器](airframes-plane.md)✅
        * [Wing Wing Z-84](airframes-plane-wing-z-84.md)✅
    * [VTOL飞行器](airframes-vtol.md)
        * [VTOL飞行器测试](airframes-vtol-testing.md)
        * [TBS Caipiroshka](airframes-vtol-caipiroshka.md)
    * [船、潜艇、飞艇、小车](airframes-experimental.md)✅
* 伙伴计算机
    * [Pixhawk伙伴计算机](pixhawk-companion-computer.md)✅
* [DroneKit机器人](robotics-using-dronekit.md)
    * [DroneKit用法](dronekit-example.md)
* [ROS机器人](robotics-using-ros.md)
    * [从外部Linux系统控制](offboard-control.md)
    * [在RPi上安装ROS](ros-raspberrypi-installation.md)
    * [MAVROS \(MAVLink on ROS\)](ros-mavros-installation.md)
    * [MAVROS外部控制示例](ros-mavros-offboard.md)
    * [从外部计算机做位置估计](external-position.md)
    * [Gazebo Octomap](simulation-gazebo-octomap.md)
* 传感器和执行机构总线
    * [I2C总线](i2c-intro.md)
        * [SF1XX激光雷达](sf1xx_lidar_setup.md)
    * [UAVCAN总线](uavcan-intro.md)
        * [UAVCAN引导器](uavcan-bootloader-installation.md)
        * [UAVCAN固件更新](uavcan-node-firmware.md)
        * [UAVCAN配置](uavcan-node-enumeration.md)
        * [UAVCAN杂记](uavcan-various-notes.md)
    * [PWM/GPIO](pwm-gpio-intro.md)
    * [UART](uart-intro.md)
        * [uLanding雷达](uart-ulanding-radar.md)
* 调试和高级主题
    * [FAQ](advanced-faq.md)
    * [系统控制台](advanced-system-console.md)
    * [系统启动过程](advanced-system-startup.md)
    * [参数和配置](advanced-configurations.md)
    * [自动驾驶仪调试](advanced-gdb-debugging.md)
    * [模拟调试](simulation-debugging.md)
    * [发送调试数值](advanced-debug-values.md)
    * [室内（伪）GPS](advanced-fake-gps.md)
    * [摄像头触发器](advanced-camera-trigger.md)
    * [日志](advanced-logging.md)
    * [飞行日志分析](flight_log_analysis.md)
    * [EKF日志回放](ekf2_log_replay.md)
    * [系统范围回放](advanced-replay.md)
    * [安装Intel RealSense R200驱动程序](advanced-realsense_intel.md)
    * [Parrot Bebop](advanced-bebop.md)
    * [云台控制设置](advanced-gimbal-control.md)
    * [如何切换状态估计器](advanced-switching_state_estimators.md)
    * [外部模块](advanced-out-of-tree-modules.md)
    * [ULog文件格式](advanced-ulog-file-format.md)
    * [Licenses](advanced-licenses.md)
* [软件更新](software_update.md)
    * [STM32引导程序](stm32_bootloader.md)
* [测试和持续集成](testing-and-ci.md)
    * [Docker容器](advanced-docker.md)
    * [持续集成](advanced-ci.md)
        * [Jenkins持续集成](advanced-jenkins-ci.md)
