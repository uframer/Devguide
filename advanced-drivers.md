# 驱动程序开发

PX4代码库使用一个轻量级的、统一的驱动抽象层：[DriverFramework](https://github.com/px4/DriverFramework)。新的POSIX驱动和[QuRT](https://en.wikipedia.org/wiki/Qualcomm_Hexagon)驱动都是在这个框架之上编写的。

<aside class="todo">
基于NuttX的传统驱动基于[Device](https://github.com/PX4/Firmware/tree/master/src/drivers/device)框架编写并且会在后面被移植到DriverFramework。
</aside>

## 核心架构

PX4是一个[反应式系统](concept-architecture.md)，使用**发布/订阅**的形式传输消息。系统的核心操作不需要使用文件句柄。两个主要的API是：

  * **发布/订阅**系统：它可以使用文件、网络或者共享内存作为后端，具体使用哪一个取决于PX4运行在哪个系统之上；
  * 全局设备注册表：可以通过它枚举设备并**读取/修改**设备的配置信息。它可能实现为一个简单的列表，或者实现为到文件系统的映射。

## 移植到一个新平台

### NuttX

  * 启动脚本位于[ROMFS/px4fmu_common](https://github.com/PX4/Firmware/tree/master/ROMFS/px4fmu_common)
  * 操作系统的配置位于[nuttx-configs](https://github.com/PX4/Firmware/tree/master/nuttx-configs)。操作系统同应用程序构建到一起并一起加载。
  * PX4中间件的配置位于[src/drivers/boards](https://github.com/PX4/Firmware/tree/master/src/drivers/boards)。其中包含总线和GPIO映射和主板的初始化代码。
  * 驱动程序位于[src/drivers](https://github.com/PX4/Firmware/tree/master/src/drivers)
  * 参考配置：运行'make px4fmu-v4_default'会按照FMUv4配置构建，它现在是NuttX的参考配置。

### QuRT/Hexagon

  * 启动脚本位于[posix-configs/](https://github.com/PX4/Firmware/tree/master/posix-configs)
  * 操作系统配置放在默认的Linux映像里（TODO: Provide location of LINUX IMAGE and flash instructions）
  * PX4中间件的配置位于[src/drivers/boards](https://github.com/PX4/Firmware/tree/master/src/drivers/boards). TODO: ADD BUS CONFIG
  * Drivers are located in [DriverFramework](https://github.com/px4/DriverFramework)
  * 参考配置：运行'make qurt_eagle_release'构建Snapdragon Flight的参考配置

## 设备ID

PX4使用*device ID*来区分每一个系统中的传感器。这些ID保存为配置参数，用于匹配传感器校准值，还用于决定将传感器的日志记入哪个日志文件中。

传感器的顺序（例如，有两个磁罗盘，分别是`/dev/mag0`和`/dev/mag1`）并没有确定的优先级 - 它们的优先级被保存在发布出来的uORB主题中。

### 解码例子

假设系统中有三个磁罗盘，用飞行日志（.px4log）转储相关参数。那么就有三个参数分别保存传感器的ID，还有一个`MAG_PRIME`参数标识哪个传感器是主传感器。每个`MAGx_ID`是一个24位的数字，最高位应该填0。


```
CAL_MAG0_ID = 73225.0
CAL_MAG1_ID = 66826.0
CAL_MAG2_ID = 263178.0
CAL_MAG_PRIME = 73225.0
```

其中，第一个传感器是通过I2C连接的外部HMC5983，它位于1号总线，在总线上的地址是`0x1E`。它在日志文件会被标记为`IMU.MagX`：

```
# device ID 73225 in 24-bit binary:
00000001  00011110  00001 001

# decodes to:
HMC5883   0x1E    bus 1 I2C
```

第二个传感器是通过SPI连接的内部HMC5983，它位于1号总线，slave select slot是5。它在日志文件中会被标记为`IMU1.MagX`：

```
# device ID 66826 in 24-bit binary:
00000001  00000101  00001 010

# decodes to:
HMC5883   dev 5   bus 1 SPI
```

第三个传感器是通过SPI连接的内部MPU9250，它位于1号总线，slave select slot是4。它在日志文件中会被标记为`IMU2.MagX`：

```
# device ID 263178 in 24-bit binary:
00000100  00000100  00001 010

#decodes to:
MPU9250   dev 4   bus 1 SPI
```

### Device ID编码

Device ID是一个24位的数字，它的格式如下。注意，第一个字段代表最低有效位。

```C
struct DeviceStructure {
  enum DeviceBusType bus_type : 3;
  uint8_t bus: 5;    // 总线实例的代码
  uint8_t address;   // 设备的总线地址（例如I2C地址）
  uint8_t devtype;   // device class之下的device type
};
```
`bus_type`的含义如下表：

```C
enum DeviceBusType {
  DeviceBusType_UNKNOWN = 0,
  DeviceBusType_I2C     = 1,
  DeviceBusType_SPI     = 2,
  DeviceBusType_UAVCAN  = 3,
};
```

`devtype`的含义则按照下表解释：

```C
#define DRV_MAG_DEVTYPE_HMC5883  0x01
#define DRV_MAG_DEVTYPE_LSM303D  0x02
#define DRV_MAG_DEVTYPE_ACCELSIM 0x03
#define DRV_MAG_DEVTYPE_MPU9250  0x04
#define DRV_ACC_DEVTYPE_LSM303D  0x11
#define DRV_ACC_DEVTYPE_BMA180   0x12
#define DRV_ACC_DEVTYPE_MPU6000  0x13
#define DRV_ACC_DEVTYPE_ACCELSIM 0x14
#define DRV_ACC_DEVTYPE_GYROSIM  0x15
#define DRV_ACC_DEVTYPE_MPU9250  0x16
#define DRV_GYR_DEVTYPE_MPU6000  0x21
#define DRV_GYR_DEVTYPE_L3GD20   0x22
#define DRV_GYR_DEVTYPE_GYROSIM  0x23
#define DRV_GYR_DEVTYPE_MPU9250  0x24
#define DRV_RNG_DEVTYPE_MB12XX   0x31
#define DRV_RNG_DEVTYPE_LL40LS   0x32
```
