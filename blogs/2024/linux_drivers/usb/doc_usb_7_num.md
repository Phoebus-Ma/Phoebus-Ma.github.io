
# USB 7-Segment Numeric Display

Manufactured by Delcom Engineering


# 1. Device Information

USB VENDOR_ID 0x0fc5 USB PRODUCT_ID 0x1227 6 字符和 8 字符显示器都有 PRODUCT_ID，并且根据 Delcom Engineering 的说法，无法从设备获取可查询的信息来区分它们。


# 2. Device Modes

默认情况下，驱动程序假定显示只有 6 个字符。6 个字符的模式为：

&emsp;MSB 0x06; LSB 0x3f

对于 8 字符显示：

&emsp;MSB 0x08; LSB 0xff

该设备可以接受原始、十六进制或 ascii 文本模式的“文本”。 raw 手动控制每个段，十六进制期望每个字符的值在 0-15 之间，ascii 期望每个字符的值在“0”-“9”和“A”-“F”之间。 默认为 ascii。


# 3. Device Operation

1. 打开设备：echo 1 > /sys/bus/usb/.../powered
2. 设置设备的模式：echo $mode_msb > /sys/bus/usb/.../mode_msb echo $mode_lsb > /sys/bus/usb/.../mode_lsb
3. 设置文本模式：echo $textmode > /sys/bus/usb/.../textmode
4. 设置文本（例如）：echo "123ABC" > /sys/bus/usb/.../text (ascii) echo "A1B2" > /sys/bus/usb/.../text (ascii) echo -ne "x01x02x03" > /sys/bus/usb/.../text (hex)
5. 设置小数位。 该设备有 6 或 8 位小数。 设置第 n 位小数 计算 10 ** n 并将其回显到 /sys/bus/usb/.../decimals 设置多个小数点将每个功率相加。 例如，要设置小数点后第 0 位和第 3 位 echo 1001 > /sys/bus/usb/.../decimals
