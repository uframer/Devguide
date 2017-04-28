# FAQ

## 构建错误

### 闪存溢出

> **提示** FMUv4架构的自动驾驶仪具有两倍大的闪存。第一款可用的FMUv4设备是[Pixracer](http://dev.px4.io/hardware-pixracer.html)。

能够加载到板载存储器中的代码的大小受限于设备闪存的大小。当添加额外的模块或者代码时，总的代码大小可能就会超过闪存大小。这会在构建时造成“闪存溢出”错误。上游的代码会保证可以通过构建通过，但是开发者自己添加的内容可能就会造成溢出。

```sh
region `flash' overflowed by 12456 bytes
```

如果要解决这个问题，要么使用新的具有更大闪存的硬件，要么删除不必要的组件。配置文件保存在[这里](https://github.com/PX4/Firmware/tree/master/cmake/configs)。如果要移除一个模块，只需要将它注释掉：

<div class="host-code"></div>

```cmake
#drivers/trone
```

## USB错误

### 无法上传固件

在Ubuntu上，卸载modem manager：

```sh
sudo apt-get remove modemmanager
```
