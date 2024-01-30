
# Authorizing (or not) your USB devices to connect to the system

Copyright (C) 2007 Inaky Perez-Gonzalez <inaky@linux.intel.com> Intel Corporation

此功能允许您控制 USB 设备是否可以在系统中使用（或不使用）。 此功能将允许您实现 USB 设备的锁定，完全由用户空间控制。

截至目前，当连接 USB 设备时，它就会被配置并且其接口立即可供用户使用。 通过此修改，只有 root 授权才能配置设备才可以使用它。


# 1. Usage

授权设备连接：

```bash
$ echo 1 > /sys/bus/usb/devices/DEVICE/authorized
```

取消设备授权：

```bash
$ echo 0 > /sys/bus/usb/devices/DEVICE/authorized
```

将连接到 hostX 的新设备设置为默认取消授权（即：锁定）：

```bash
$ echo 0 > /sys/bus/usb/devices/usbX/authorized_default
```

解除锁定：

```bash
$ echo 1 > /sys/bus/usb/devices/usbX/authorized_default
```

默认情况下，所有 USB 设备都被授权。 将“2”写入authorized_default属性会导致内核默认仅授权连接到内部USB端口的设备。


# 2. Example system lockdown (lame)

想象一下，您想要实施锁定，以便只能连接 XYZ 类型的设备（例如，它是具有可见 USB 端口的信息亭机器）：

```bash
boot up
rc.local ->

 for host in /sys/bus/usb/devices/usb*
 do
    echo 0 > $host/authorized_default
 done
```

对于新的 USB 设备，将脚本连接到 udev：

```bash
if device_is_my_type $DEV
then
  echo 1 > $device_path/authorized
done
```

现在，device_is_my_type() 是锁定的源泉。 仅检查类、类型和协议是否匹配某些内容是您可以进行的最差的安全验证（或者对于愿意破坏它的人来说是最好的）。 如果您需要安全的东西，请使用加密和证书身份验证或类似的东西。 对于存储密钥来说，简单的事情可能是：

```bash
function device_is_my_type()
{
  echo 1 > authorized          # temporarily authorize it
                               # FIXME: make sure none can mount it
  mount DEVICENODE /mntpoint
  sum=$(md5sum /mntpoint/.signature)
  if [ $sum = $(cat /etc/lockdown/keysum) ]
  then
       echo "We are good, connected"
       umount /mntpoint
       # Other stuff so others can use it
  else
       echo 0 > authorized
  fi
}
```

当然，这是蹩脚的，您想要使用 PKI 进行真正的证书验证，这样您就不会依赖于共享秘密等，但您明白了。 任何有权访问设备小工具套件的人都可以伪造描述符和设备信息。 不要相信那个。 不客气。


# 3. Interface authorization

有类似的方法来允许或拒绝特定 USB 接口。 这允许仅阻止 USB 设备的子集。

授权接口：

```bash
$ echo 1 > /sys/bus/usb/devices/INTERFACE/authorized
```

取消接口授权：

```bash
$ echo 0 > /sys/bus/usb/devices/INTERFACE/authorized
```

特定 USB 总线上新接口的默认值也可以更改。

默认允许接口：

```bash
$ echo 1 > /sys/bus/usb/devices/usbX/interface_authorized_default
```

默认拒绝接口：

```bash
$ echo 0 > /sys/bus/usb/devices/usbX/interface_authorized_default
```

默认情况下，interface_authorized_default 位为 1。因此所有接口都会默认授权。

笔记：

如果要对未授权的接口进行授权，则必须通过将 INTERFACE 写入 /sys/bus/usb/drivers_probe 来手动触发驱动程序探测。

对于需要多个接口的驱动程序，应首先对所有需要的接口进行授权。 之后，应对司机进行调查。 这可以避免副作用。
