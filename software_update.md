# 软件更新

更新PX4软件的方法依赖于具体的硬件平台。对于基于微控制器的系统来说，新的固件是通过USB或者串口烧写的。


## 基础架构

### 烧写引导程序，

```bash
arm-none-eabi-gdb
  (gdb) tar ext /dev/serial/by-id/usb-Black_Sphere_XXX-if00
  (gdb) mon swdp_scan
  (gdb) attach 1
  (gdb) load tapv1_bl.elf
        ...
        Transfer rate: 17 KB/sec, 828 bytes/write.
  (gdb) kill
```
