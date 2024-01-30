
# Linux UVC Gadget Driver

# 1. Overview

UVC Gadget 驱动程序是 USB 连接设备端硬件的驱动程序。 它旨在在具有 USB 设备端硬件（例如带有 OTG 端口的板）的 Linux 系统上运行。

在设备系统上，一旦绑定了驱动程序，它就会显示为具有输出功能的 V4L2 设备。

在主机端（一旦通过 USB 电缆连接），运行 UVC Gadget 驱动程序并由适当的用户空间程序控制的设备应显示为符合 UVC 规范的相机，并与任何旨在处理它们的程序一起正常运行。 设备系统上运行的用户空间程序可以对来自各种来源的图像缓冲区进行排队，以便通过 USB 连接进行传输。 通常，这意味着从相机传感器外设转发缓冲区，但缓冲区的来源完全取决于用户空间配套程序。


# 2. Configuring the device kernel

必须选择 Kconfig 选项 USB_CONFIGFS、USB_LIBCOMPOSITE、USB_CONFIGFS_F_UVC 和 USB_F_UVC 才能启用对 UVC 小工具的支持。


# 3. Configuring the gadget through configfs

UVC Gadget 期望使用 UVC 功能通过 configfs 进行配置。 这提供了很大程度的灵活性，因为许多 UVC 设备的设置都可以通过这种方式进行控制。

此处并未描述所有可用属性。 有关完整枚举，请参阅 Documentation/ABI/testing/configfs-usb-gadget-uvc。


## 3.1 Assumptions

本节假设您已在 /sys/kernel/config 挂载 configfs 并创建了一个小工具作为 /sys/kernel/config/usb_gadget/g1。


# 4. The UVC Function

第一步是创建 UVC 函数：

```bash
# These variables will be assumed throughout the rest of the document
CONFIGFS="/sys/kernel/config"
GADGET="$CONFIGFS/usb_gadget/g1"
FUNCTION="$GADGET/functions/uvc.0"

mkdir -p $FUNCTION
```


# 5. Formats and Frames

您必须通过告诉小工具您支持的格式以及每种格式支持的帧大小和帧间隔来配置小工具。 在当前的实现中，小工具无法拒绝设置主机指示其设置的格式，因此准确完成此步骤以确保主机永远不会要求无法设置的格式非常重要。 假如。

格式是在streaming/uncompressed和streaming/mjpeg configfs组下创建的，帧大小是在以下结构的格式下创建的：

```bash
uvc.0 +
      |
      + streaming +
                  |
                  + mjpeg +
                  |       |
                  |       + mjpeg +
                  |            |
                  |            + 720p
                  |            |
                  |            + 1080p
                  |
                  + uncompressed +
                                 |
                                 + yuyv +
                                        |
                                        + 720p
                                        |
                                        + 1080p
```

然后可以配置每个帧的宽度和高度，加上存储单个帧所需的最大缓冲区大小，最后配置该格式和帧大小支持的帧间隔。 宽度和高度以像素为单位枚举，帧间隔以100ns为单位。 例如，要为每个帧大小创建具有 2、15 和 100 fps 帧间隔的上述结构，您可以执行以下操作：

```bash
create_frame() {
        # Example usage:
        # create_frame <width> <height> <group> <format name>

        WIDTH=$1
        HEIGHT=$2
        FORMAT=$3
        NAME=$4

        wdir=$FUNCTION/streaming/$FORMAT/$NAME/${HEIGHT}p

        mkdir -p $wdir
        echo $WIDTH > $wdir/wWidth
        echo $HEIGHT > $wdir/wHeight
        echo $(( $WIDTH * $HEIGHT * 2 )) > $wdir/dwMaxVideoFrameBufferSize
        cat <<EOF > $wdir/dwFrameInterval
666666
100000
5000000
EOF
}

create_frame 1280 720 mjpeg mjpeg
create_frame 1920 1080 mjpeg mjpeg
create_frame 1280 720 uncompressed yuyv
create_frame 1920 1080 uncompressed yuyv
```

目前唯一支持的未压缩格式是 YUYV，详细信息请参见 [Packed YUV formats](https://www.kernel.org/doc/html/latest/userspace-api/media/v4l/pixfmt-packed-yuv.html).


# 6. Color Matching Descriptors

可以为您创建的每种格式指定一些色度信息。 此步骤为可选，跳过此步骤将包含默认信息； 这些默认值遵循 UVC 规范的颜色匹配描述符部分中定义的值。

要创建颜色匹配描述符，请创建一个 configfs 项并将其三个属性设置为您所需的设置，然后从您希望与其关联的格式链接到它：

```bash
# Create a new Color Matching Descriptor

mkdir $FUNCTION/streaming/color_matching/yuyv
pushd $FUNCTION/streaming/color_matching/yuyv

echo 1 > bColorPrimaries
echo 1 > bTransferCharacteristics
echo 4 > bMatrixCoefficients

popd

# Create a symlink to the Color Matching Descriptor from the format's config item
ln -s $FUNCTION/streaming/color_matching/yuyv $FUNCTION/streaming/uncompressed/yuyv
```

有关有效值的详细信息，请参阅 UVC 规范。 请注意，存在默认颜色匹配描述符，并且任何不具有指向不同颜色匹配描述符的链接的格式都可以使用该默认颜色匹配描述符。 可以更改默认描述符的属性设置，因此请记住，如果您这样做，您将更改不链接到其他格式的任何格式的默认值。


# 7. Header linking

UVC 规范要求格式和帧描述符前面有标头，详细说明后面的不同格式描述符的数量和累积大小等信息。 通过在表示标头的 configfs 项和表示其他描述符的配置项之间进行链接，在 configfs 中实现此操作和类似的操作，如下所示：

```bash
mkdir $FUNCTION/streaming/header/h

# This section links the format descriptors and their associated frames
# to the header
cd $FUNCTION/streaming/header/h
ln -s ../../uncompressed/yuyv
ln -s ../../mjpeg/mjpeg

# This section ensures that the header will be transmitted for each
# speed's set of descriptors. If support for a particular speed is not
# needed then it can be skipped here.
cd ../../class/fs
ln -s ../../header/h
cd ../../class/hs
ln -s ../../header/h
cd ../../class/ss
ln -s ../../header/h
cd ../../../control
mkdir header/h
ln -s header/h class/fs
ln -s header/h class/ss
```


# 8. Extension Unit Support

UVC 扩展单元 (XU) 基本上提供了一个可以处理控制设置和获取请求的独特单元。 这些控制请求的含义完全取决于实现，但可用于控制 UVC 规范之外的设置（例如启用或禁用视频效果）。 XU 可以插入 UVC 单元链或自由悬挂。

配置扩展单元涉及在适当的目录中创建一个条目并适当地设置其属性，如下所示：

```bash
mkdir $FUNCTION/control/extensions/xu.0
pushd $FUNCTION/control/extensions/xu.0

# Set the bUnitID of the Processing Unit as the source for this
# Extension Unit
echo 2 > baSourceID

# Set this XU as the source of the default output terminal. This inserts
# the XU into the UVC chain between the PU and OT such that the final
# chain is IT > PU > XU.0 > OT
cat bUnitID > ../../terminal/output/default/baSourceID

# Flag some controls as being available for use. The bmControl field is
# a bitmap with each bit denoting the availability of a particular
# control. For example to flag the 0th, 2nd and 3rd controls available:
echo 0x0d > bmControls

# Set the GUID; this is a vendor-specific code identifying the XU.
echo -e -n "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10" > guidExtensionCode

popd
```

bmControls 属性和baSourceID 属性是多值属性。 这意味着您可以向它们写入多个换行分隔值。 例如，要将第 1 个、第 2 个、第 9 个和第 10 个控件标记为可用，您需要向 bmControls 写入两个值，如下所示：

```bash
cat << EOF > bmControls
0x03
0x03
EOF
```

baSourceID 属性的多值性质掩盖了 XU 可以是多输入的事实，但请注意，这目前没有显着影响。

bControlSize 属性反映 bmControls 属性的大小，类似地，bNrInPins 反映 baSourceID 属性的大小。 当您设置 bmControls 和 baSourceID 时，这两个属性都会自动增加/减少。 还可以手动增加或减少 bControlSize，其效果是将条目截断为新大小，或者用 0x00 填充条目，例如：

```bash
$ cat bmControls
0x03
0x05

$ cat bControlSize
2

$ echo 1 > bControlSize
$ cat bmControls
0x03

$ echo 2 > bControlSize
$ cat bmControls
0x03
0x00
```

bNrInPins 和 baSourceID 的功能相同。


# 9. Configuring Supported Controls for Camera Terminal and Processing Unit

UVC 链中的相机终端和处理单元也具有 bmControls 属性，其功能与扩展单元中的相同字段类似。 然而，与 XU 不同的是，这些单元的位标志的含义是在 UVC 规范中定义的； 您应该查阅“相机终端描述符”和“处理单元描述符”部分以获取标志的枚举。

```bash
# Set the Processing Unit's bmControls, flagging Brightness, Contrast
# and Hue as available controls:
echo 0x05 > $FUNCTION/control/processing/default/bmControls

# Set the Camera Terminal's bmControls, flagging Focus Absolute and
# Focus Relative as available controls:
echo 0x60 > $FUNCTION/control/terminal/camera/default/bmControls
```

如果您不设置这些字段，则默认情况下，相机终端的自动曝光模式控制和处理单元的亮度控制将被标记为可用； 如果不支持，您应该将该字段设置为 0x00。

请注意，相机终端或处理单元的 bmControls 字段的大小由 UVC 规范固定，因此 bControlSize 属性在这里是只读的。


# 10. Custom Strings Support

为 USB 设备的各个部分提供文本描述的字符串描述符可以在 USB configfs 中的常用位置定义，然后可以从 UVC 函数根或扩展单元目录链接到，以将这些字符串分配为描述符：

```bash
# Create a string descriptor in us-EN and link to it from the function
# root. The name of the link is significant here, as it declares this
# descriptor to be intended for the Interface Association Descriptor.
# Other significant link names at function root are vs0_desc and vs1_desc
# For the VideoStreaming Interface 0/1 Descriptors.

mkdir -p $GADGET/strings/0x409/iad_desc
echo -n "Interface Associaton Descriptor" > $GADGET/strings/0x409/iad_desc/s
ln -s $GADGET/strings/0x409/iad_desc $FUNCTION/iad_desc

# Because the link to a String Descriptor from an Extension Unit clearly
# associates the two, the name of this link is not significant and may
# be set freely.

mkdir -p $GADGET/strings/0x409/xu.0
echo -n "A Very Useful Extension Unit" > $GADGET/strings/0x409/xu.0/s
ln -s $GADGET/strings/0x409/xu.0 $FUNCTION/control/extensions/xu.0
```


# 11. The interrupt endpoint

VideoControl 接口有一个可选的中断端点，默认情况下禁用该端点。 这是为了支持 UVC 的延迟响应控制集请求（它应该通过中断端点响应而不是占用端点 0）。 目前缺少通过此端点发送数据的支持，因此它被禁用以避免混淆。 如果您想启用它，可以通过 configfs 属性来实现：

```bash
echo 1 > $FUNCTION/control/enable_interrupt_ep
```


# 12. Bandwidth configuration

共有三个属性控制 USB 连接的带宽。 它们位于函数根目录中，可以在限制范围内设置：

```bash
# streaming_interval sets bInterval. Values range from 1..255
echo 1 > $FUNCTION/streaming_interval

# streaming_maxpacket sets wMaxPacketSize. Valid values are 1024/2048/3072
echo 3072 > $FUNCTION/streaming_maxpacket

# streaming_maxburst sets bMaxBurst. Valid values are 1..15
echo 1 > $FUNCTION/streaming_maxburst
```

此处传递的值将根据 UVC 规范（取决于 USB 连接的速度）被限制为有效值。 要了解这些设置如何影响带宽，您应该查阅 UVC 规范，但经验法则是增加 Streaming_MaxPacket 设置将提高带宽（从而提高最大可能帧速率），而如果 USB 连接正在运行，则 Streaming_MaxBurst 也是如此 以超高速。 增加streaming_interval将减少带宽和帧速率。


# 13. The userspace application

UVC Gadget 驱动程序本身无法执行任何特别有趣的操作。 它必须与用户空间程序配对，该程序响应 UVC 控制请求并填充缓冲区以排队到驱动程序创建的 V4L2 设备。 如何实现这些事情取决于实现，超出了本文档的范围，但可以在以下位置找到参考应用程序： <https://gitlab.freedesktop.org/camera/uvc-gadget>。
