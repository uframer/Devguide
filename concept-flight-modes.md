# 飞行模式

**飞行模式**定义了系统的状态。用户可以通过遥控器上的开关或者[地面站](qgroundcontrol-intro.md)切换飞行模式。

## 飞行模式简介

  * **_MANUAL_**
    * **固定翼飞行器/车/船：**
        * **MANUAL:** 飞行员的控制输入会被直接传递给输出混控器。
        * **STABILIZED:** 飞行员的滚转、俯仰输入会被作为*角度（angle）*命令，偏航则直接传递给输出混控器。
    * **多轴飞行器：**
        * **ACRO:** 飞行员的滚转、俯仰、偏航输入会被作为速率（rate）命令发送给自动驾驶仪。在这种模式下你可以实现倒飞。油门输入会被直接传递给输出混控器。
        * **RATTITUDE** 如果高于该模式指定的阈值，那么飞行员的滚转、俯仰、偏航输入会被作为*速率（rate）*命令发送给自动驾驶仪；否则，滚转、俯仰输入会被作为*角度（angle）*命令传递，而偏航输入则作为*速率（rate）*命令传递。油门输入被直接传递给输出混控器。
        * **ANGLE** 飞行员的滚转和俯仰输入会被传递为*角度（angle）*命令，偏航输入会被传递为*速率（rate）*命令。油门输入会直接传递给输出混控器。
  * **_ASSISTED_**
    * **ALTCTL**
      * **固定翼飞行器：** 如果滚转、俯仰和偏航输入都为0（位于某个死区内），那么飞机会恢复到水平直线飞行的状态并维持在当前的高度。如果风速不为零，那么会随风漂移。
      * **多轴飞行器：** Roll, pitch and yaw inputs are as in MANUAL mode. Throttle inputs indicate climb or sink at a predetermined maximum rate. Throttle has large deadzone.
    * **POSCTL**
      * **固定翼飞行器：** Neutral inputs give level, flight and it will crab against the wind if needed to maintain a straight line.
      * **多轴飞行器：** Roll controls left-right speed, pitch controls front-back speed over ground. When roll and pitch are all centered (inside deadzone) the multirotor will hold position. Yaw controls yaw rate as in MANUAL mode. Throttle controls climb/descent rate as in ALTCTL mode.
  * **_AUTO_**
    * **AUTO_LOITER（悬停模式/徘徊模式）**
        * **固定翼飞行器：** 飞机会在当前高度、围绕当前的位置徘徊（也有可能稍微高于当前高度，要飞丢了时可以切到这个模式）。
        * **多轴飞行器：**  多轴飞行器会悬停/徘徊在当前的位置和高度。
    * **AUTO_RTL（返航模式）**
        * **固定翼飞行器：** 飞机返回返航点并在空中环绕返航点徘徊。
        * **多轴飞行器：** 多轴飞行器以当前高度（如果当前高度高于返航点的高度及悬停模式的高度）或者悬停模式的高度（如果当前高度低于悬停模式的高度）沿直线返回返航点，然后自动降落。
    * **AUTO_MISSION**
        * **所有系统类型：** 飞机遵循地面站发来的命令。如果没有任务，那么飞机回在当前位置进入LOITER模式。
  * **_OFFBOARD_**
    在这个模式下，位置、速度或者高度的参考值、目标值、setpoint都由伙伴计算机通过串口+MAVLink发送过来。The offboard setpoint can be provided by APIs like [MAVROS](https://github.com/mavlink/mavros) or [Dronekit](http://dronekit.io).

## 飞行模式评估图

你可以用这张图来判断自己需要哪种飞行模式：

![](images/diagrams/commander-flow-diagram.png)
