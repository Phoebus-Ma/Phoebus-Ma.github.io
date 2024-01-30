
# ChipIdea Highspeed Dual Role Controller Driver

# 1. How to test OTG FSM(HNP and SRP)

展示如何使用 2 个 Freescale i.MX6Q sabre SD 板通过系统输入文件演示 OTG HNP 和 SRP 功能。


## 1.1 How to enable OTG FSM

### 1.1.1 Select CONFIG_USB_OTG_FSM in menuconfig, rebuild kernel

图像和模块。 如果您想检查 otg fsm 的一些内部变量，请挂载 debugfs，有 2 个文件可以显示 otg fsm 变量和一些控制器寄存器值：

```bash
cat /sys/kernel/debug/ci_hdrc.0/otg
cat /sys/kernel/debug/ci_hdrc.0/registers
```


### 1.1.2 Add below entries in your dts file for your controller node

```bash
otg-rev = <0x0200>;
adp-disable;
```


## 1.2 Test operations

启动 2 个 Freescale i.MX6Q sabre SD 板，并加载小工具类驱动程序（例如 g_mass_storage）。

用 USB 线连接 2 个板：一端是 micro A 插头，另一端是 micro B 插头。

A 设备（插入微型 A 插头）应枚举 B 设备。

角色切换：

在 B-device:

```bash
echo 1 > /sys/bus/platform/devices/ci_hdrc.0/inputs/b_bus_req
```

B 设备应承担主机角色并枚举 A 设备。

A 设备切换回主机。

在 B-device:

```bash
echo 0 > /sys/bus/platform/devices/ci_hdrc.0/inputs/b_bus_req
```

或者，通过引入HNP轮询，B-Host可以知道A-peripheral何时希望成为主机角色，因此A-peripheral侧也可以通过回答B-Host的轮询来触发此角色切换。 这可以在 A 设备上完成：

```bash
echo 1 > /sys/bus/platform/devices/ci_hdrc.0/inputs/a_bus_req
```

A 设备应切换回主机并枚举 B 设备。

移除B设备（拔下微型B插头）并在10秒内再次插入； A 设备应再次枚举 B 设备。

取下B设备（拔掉micro B插头），10秒后再次插入； A 设备不应枚举 B 设备。

如果A设备想要使用总线：

在 A-device:

```bash
echo 0 > /sys/bus/platform/devices/ci_hdrc.0/inputs/a_bus_drop
echo 1 > /sys/bus/platform/devices/ci_hdrc.0/inputs/a_bus_req
```

如果B设备想要使用总线：

在 B-device:

```bash
echo 1 > /sys/bus/platform/devices/ci_hdrc.0/inputs/b_bus_req
```

A 设备关闭总线电源。

在 A-device:

```bash
echo 1 > /sys/bus/platform/devices/ci_hdrc.0/inputs/a_bus_drop
```

A 设备应与 B 设备断开连接并关闭总线电源。

B 设备为 SRP 提供数据脉冲。

在 B-device:

```bash
echo 1 > /sys/bus/platform/devices/ci_hdrc.0/inputs/b_bus_req
```

A 设备应恢复 USB 总线并枚举 B 设备。


## 1.3 Reference document

“USB 修订版 2.0 规范的移动和嵌入式主机补充，2012 年 7 月 27 日修订版 2.0 版本 1.1a”。


# 2. How to enable USB as system wakeup source

以下是如何在 imx6 平台上启用 USB 作为系统唤醒源的示例。


## 2.1 Enable core's wakeup:

```bash
echo enabled > /sys/bus/platform/devices/ci_hdrc.0/power/wakeup
```


## 2.2 Enable glue layer's wakeup:

```bash
echo enabled > /sys/bus/platform/devices/2184000.usb/power/wakeup
```


## 2.3 Enable PHY's wakeup (optional):

```bash
echo enabled > /sys/bus/platform/devices/20c9000.usbphy/power/wakeup
```


## 2.4 Enable roothub's wakeup:

```bash
echo enabled > /sys/bus/usb/devices/usb1/power/wakeup
```


## 2.5 Enable related device's wakeup:

```bash
echo enabled > /sys/bus/usb/devices/1-1/power/wakeup
```

如果系统只有一个 USB 端口，并且您希望在该端口进行 USB 唤醒，则可以使用以下脚本来启用 USB 唤醒：

```bash
for i in $(find /sys -name wakeup | grep usb);do echo enabled > $i;done;
```
