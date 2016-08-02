# 架构概览

PX4有两个主要的层次组成：[PX4飞行软件栈](concept-flight-stack.md) - 一个自动驾驶仪软件解决方案；[PX4中间件](concept-middleware.md) - 一个通用的机器人中间件，可以支持任意类型的自主机器人。

所有的[机架](airframes-architecture.md)，甚至所有的机器人系统，如船，都共享同一份代码。整个系统采用[响应式](http://www.reactivemanifesto.org)设计，这意味着：

  * 所有的功能都被划分为可替换的组件
  * 通过异步消息传递通信
  * 系统可以处理各种各样的负载

除了这些运行时的考虑，模块化还大大提高了[重用性](https://en.wikipedia.org/wiki/Reusability)。

## 高层软件架构

下面每一个方框代表一个独立的模块，每一个箭头表示通过[uORB](advanced-uorb.md)的publish/subscribe调用实现的连接。

> **信息** PX4的架构允许你非常方便地替换每一个模块，甚至可以在运行时实现这一替换。

每个控制器/混控器都针对特定类型的机架（例如，多轴飞行器、VTOL飞行器或者固定翼飞行器），但是高层的任务管理模块如`commander`和`navigator`在各个平台之间通用。

{% mermaid %}
graph TD;
  commander-->navigator;
  user-->commander;
  user-->stickmapper;
  stickmapper-->navigator;
  navigator-->pos_ctrl
  pos_ctrl-->att_ctrl;
  att_ctrl-->mixer;
  position_estimator-->pos_ctrl;
  position_estimator-->navigator;
  position_estimator-->attitude_estimator;
  attitude_estimator-->att_ctrl;
  mixer-->motor_driver;
{% endmermaid %}

## 地面站通讯架构

The interaction with the ground control station (GCS) is handled through the "business logic" applications including the commander (general command & control, e.g. arming), the navigator (accepts missions and turns them into lower-level navigation primitives) and the mavlink application, which accepts MAVLink packets and converts them into the onboard uORB data structures. This isolation has been architected explicitly to avoid having a MAVLink dependency deep in the system. The MAVLink application also consumes a lot of sensor data and state estimates and sends them to the ground control station.

{% mermaid %}
graph TD;
  mavlink---commander;
  mavlink---navigator;
  position_estimator-->mavlink;
  attitude_estimator-->mavlink;
  mixer-->mavlink;
{% endmermaid %}
