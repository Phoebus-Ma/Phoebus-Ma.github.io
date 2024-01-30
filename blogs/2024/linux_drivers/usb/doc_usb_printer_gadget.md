
# Linux USB Printer Gadget Driver

06/04/2007

Copyright (C) 2007 Craig W. Nadler <craig@nadler.us>


# 1. General

如果您使用 Linux 作为嵌入式操作系统编写打印机固件，则可以使用此驱动程序。 该驱动程序与在 Linux 主机系统上使用打印机无关。

您将需要一个 USB 设备控制器和一个 Linux 驱动程序，该驱动程序使用 Linux USB Gadget API 接受小工具/“设备类”驱动程序。 加载USB设备控制器驱动程序后，加载打印机小工具驱动程序。 这将为您的 USB 设备端口所连接的 USB 主机提供一个打印机接口。

该驱动程序是为在用户模式下运行的打印机固件构建的。 用户模式打印机固件将使用设备文件从内核模式打印机小工具驱动程序读取和写入数据。 当 USB HOST 发送设备请求以获取打印机状态时，打印机返回打印机状态字节。 用户空间固件可以使用设备文件 /dev/g_printer 读取或写入此状态字节。 支持阻塞和非阻塞读/写调用。


# 2. Howto Use This Driver

加载 USB 设备控制器驱动程序和打印机小工具驱动程序。 以下示例使用 Netchip 2280 USB 设备控制器驱动程序：

```bash
modprobe net2280
modprobe g_printer
```

加载打印机小工具时可以使用以下命令行参数（例如： modprobe g_printer idVendor=0x0525 idProduct=0xa4a8 ）：

**idVendor**

这是设备描述符中使用的供应商 ID。 默认值为 Netchip 供应商 ID 0x0525。 在发布产品之前，您必须更改为您自己的供应商 ID。 如果您计划发布产品但还没有供应商 ID，请访问 www.usb.org 了解如何获取供应商 ID 的详细信息。

**idProduct**

这是设备描述符中使用的产品 ID。 默认值为 0xa4a8，您应该将其更改为任何其他 USB 产品（如果有）未使用的 ID。 最好从 0x0001 开始对产品进行编号。

**bcdDevice**

这是您产品的版本号。 最好将您的固件版本放在这里。

**iManufacturer**

包含供应商名称的字符串。

**iProduct**

包含产品名称的字符串。

**iSerialNum**

包含序列号的字符串。 这应该针对您产品的每个单元进行更改。

**iPNPstring**

用于该打印机的 PNP ID 字符串。 您需要在命令行上设置或硬编码用于打印机产品的 PNP ID 字符串。

**qlen**

每个端点使用的 8k 缓冲区数量。 默认值为 10，您应该根据您的产品调整此值。 您可能还想调整产品的每个缓冲区的大小。


# 3. Using The Example Code

此示例代码与标准输出通信，而不是与打印引擎通信。

编译以下测试代码：

1. 将其保存到名为 prn_example.c 的文件中

2. 使用以下命令编译代码：

```bash
gcc prn_example.c -o prn_example
```

要将打印机数据从主机读取到标准输出：

```bash
# prn_example -read_data
```

要将打印机数据从文件 (data_file) 写入主机：

```bash
# cat data_file | prn_example -write_data
```

要获取小工具驱动程序的当前打印机状态：

```bash
# prn_example -get_status

Printer status is:
     Printer is NOT Selected
     Paper is Out
     Printer OK
```

要将打印机设置为“选定/在线”：

```bash
# prn_example -selected
```

要将打印机设置为未选择/离线：

```bash
# prn_example -not_selected
```

将纸张状态设置为缺纸：

```bash
# prn_example -paper_out
```

要将纸张状态设置为已装入纸张：

```bash
# prn_example -paper_loaded
```

要将错误状态设置为打印机正常：

```bash
# prn_example -no_error
```

要将错误状态设置为 ERROR：

```bash
# prn_example -error
```


# 4. Example Code

```c
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>
#include <linux/poll.h>
#include <sys/ioctl.h>
#include <linux/usb/g_printer.h>

#define PRINTER_FILE                  "/dev/g_printer"
#define BUF_SIZE                      512


/*
 * 'usage()' - Show program usage.
 */

static void
usage(const char *option)             /* I - Option string or NULL */
{
    if (option) {
        fprintf(stderr,"prn_example: Unknown option \"%s\"!\n",
                option);
    }

    fputs("\n", stderr);
    fputs("Usage: prn_example -[options]\n", stderr);
    fputs("Options:\n", stderr);
    fputs("\n", stderr);
    fputs("-get_status    Get the current printer status.\n", stderr);
    fputs("-selected      Set the selected status to selected.\n", stderr);
    fputs("-not_selected  Set the selected status to NOT selected.\n",
                    stderr);
    fputs("-error         Set the error status to error.\n", stderr);
    fputs("-no_error      Set the error status to NO error.\n", stderr);
    fputs("-paper_out     Set the paper status to paper out.\n", stderr);
    fputs("-paper_loaded  Set the paper status to paper loaded.\n",
                    stderr);
    fputs("-read_data     Read printer data from driver.\n", stderr);
    fputs("-write_data    Write printer sata to driver.\n", stderr);
    fputs("-NB_read_data  (Non-Blocking) Read printer data from driver.\n",
                    stderr);
    fputs("\n\n", stderr);

    exit(1);
}


static int
read_printer_data()
{
    struct pollfd   fd[1];

    /* Open device file for printer gadget. */
    fd[0].fd = open(PRINTER_FILE, O_RDWR);
    if (fd[0].fd < 0) {
        printf("Error %d opening %s\n", fd[0].fd, PRINTER_FILE);
        close(fd[0].fd);
        return(-1);
    }

    fd[0].events = POLLIN | POLLRDNORM;

    while (1) {
        static char buf[BUF_SIZE];
        int bytes_read;
        int retval;

        /* Wait for up to 1 second for data. */
        retval = poll(fd, 1, 1000);

        if (retval && (fd[0].revents & POLLRDNORM)) {

            /* Read data from printer gadget driver. */
            bytes_read = read(fd[0].fd, buf, BUF_SIZE);

            if (bytes_read < 0) {
                printf("Error %d reading from %s\n",
                                fd[0].fd, PRINTER_FILE);
                close(fd[0].fd);
                return(-1);
            } else if (bytes_read > 0) {
                /* Write data to standard OUTPUT (stdout). */
                fwrite(buf, 1, bytes_read, stdout);
                fflush(stdout);
            }
        }
    }

    /* Close the device file. */
    close(fd[0].fd);

    return 0;
}


static int
write_printer_data()
{
    struct pollfd   fd[1];

    /* Open device file for printer gadget. */
    fd[0].fd = open (PRINTER_FILE, O_RDWR);
    if (fd[0].fd < 0) {
        printf("Error %d opening %s\n", fd[0].fd, PRINTER_FILE);
        close(fd[0].fd);
        return(-1);
    }

    fd[0].events = POLLOUT | POLLWRNORM;

    while (1) {
        int retval;
        static char buf[BUF_SIZE];
        /* Read data from standard INPUT (stdin). */
        int bytes_read = fread(buf, 1, BUF_SIZE, stdin);

        if (!bytes_read) {
            break;
        }

        while (bytes_read) {

            /* Wait for up to 1 second to sent data. */
            retval = poll(fd, 1, 1000);

            /* Write data to printer gadget driver. */
            if (retval && (fd[0].revents & POLLWRNORM)) {
                retval = write(fd[0].fd, buf, bytes_read);
                if (retval < 0) {
                    printf("Error %d writing to %s\n",
                                    fd[0].fd,
                                    PRINTER_FILE);
                    close(fd[0].fd);
                    return(-1);
                } else {
                    bytes_read -= retval;
                }
            }
        }
    }

    /* Wait until the data has been sent. */
    fsync(fd[0].fd);

    /* Close the device file. */
    close(fd[0].fd);

    return 0;
}


static int
read_NB_printer_data()
{
    int             fd;
    static char     buf[BUF_SIZE];
    int             bytes_read;

    /* Open device file for printer gadget. */
    fd = open(PRINTER_FILE, O_RDWR|O_NONBLOCK);
    if (fd < 0) {
        printf("Error %d opening %s\n", fd, PRINTER_FILE);
        close(fd);
        return(-1);
    }

    while (1) {
        /* Read data from printer gadget driver. */
        bytes_read = read(fd, buf, BUF_SIZE);
        if (bytes_read <= 0) {
                break;
        }

        /* Write data to standard OUTPUT (stdout). */
        fwrite(buf, 1, bytes_read, stdout);
        fflush(stdout);
    }

    /* Close the device file. */
    close(fd);

    return 0;
}


static int
get_printer_status()
{
    int     retval;
    int     fd;

    /* Open device file for printer gadget. */
    fd = open(PRINTER_FILE, O_RDWR);
    if (fd < 0) {
        printf("Error %d opening %s\n", fd, PRINTER_FILE);
        close(fd);
        return(-1);
    }

    /* Make the IOCTL call. */
    retval = ioctl(fd, GADGET_GET_PRINTER_STATUS);
    if (retval < 0) {
        fprintf(stderr, "ERROR: Failed to set printer status\n");
        return(-1);
    }

    /* Close the device file. */
    close(fd);

    return(retval);
}


static int
set_printer_status(unsigned char buf, int clear_printer_status_bit)
{
    int     retval;
    int     fd;

    retval = get_printer_status();
    if (retval < 0) {
        fprintf(stderr, "ERROR: Failed to get printer status\n");
        return(-1);
    }

    /* Open device file for printer gadget. */
    fd = open(PRINTER_FILE, O_RDWR);

    if (fd < 0) {
        printf("Error %d opening %s\n", fd, PRINTER_FILE);
        close(fd);
        return(-1);
    }

    if (clear_printer_status_bit) {
        retval &= ~buf;
    } else {
        retval |= buf;
    }

    /* Make the IOCTL call. */
    if (ioctl(fd, GADGET_SET_PRINTER_STATUS, (unsigned char)retval)) {
        fprintf(stderr, "ERROR: Failed to set printer status\n");
        return(-1);
    }

    /* Close the device file. */
    close(fd);

    return 0;
}


static int
display_printer_status()
{
    char    printer_status;

    printer_status = get_printer_status();
    if (printer_status < 0) {
        fprintf(stderr, "ERROR: Failed to get printer status\n");
        return(-1);
    }

    printf("Printer status is:\n");
    if (printer_status & PRINTER_SELECTED) {
        printf("     Printer is Selected\n");
    } else {
        printf("     Printer is NOT Selected\n");
    }
    if (printer_status & PRINTER_PAPER_EMPTY) {
        printf("     Paper is Out\n");
    } else {
        printf("     Paper is Loaded\n");
    }
    if (printer_status & PRINTER_NOT_ERROR) {
        printf("     Printer OK\n");
    } else {
        printf("     Printer ERROR\n");
    }

    return(0);
}


int
main(int  argc, char *argv[])
{
    int     i;              /* Looping var */
    int     retval = 0;

    /* No Args */
    if (argc == 1) {
        usage(0);
        exit(0);
    }

    for (i = 1; i < argc && !retval; i ++) {

        if (argv[i][0] != '-') {
            continue;
        }

        if (!strcmp(argv[i], "-get_status")) {
            if (display_printer_status()) {
                retval = 1;
            }

        } else if (!strcmp(argv[i], "-paper_loaded")) {
            if (set_printer_status(PRINTER_PAPER_EMPTY, 1)) {
                retval = 1;
            }

        } else if (!strcmp(argv[i], "-paper_out")) {
            if (set_printer_status(PRINTER_PAPER_EMPTY, 0)) {
                retval = 1;
            }

        } else if (!strcmp(argv[i], "-selected")) {
            if (set_printer_status(PRINTER_SELECTED, 0)) {
                retval = 1;
            }

        } else if (!strcmp(argv[i], "-not_selected")) {
            if (set_printer_status(PRINTER_SELECTED, 1)) {
                retval = 1;
            }

        } else if (!strcmp(argv[i], "-error")) {
            if (set_printer_status(PRINTER_NOT_ERROR, 1)) {
                retval = 1;
            }

        } else if (!strcmp(argv[i], "-no_error")) {
            if (set_printer_status(PRINTER_NOT_ERROR, 0)) {
                retval = 1;
            }

        } else if (!strcmp(argv[i], "-read_data")) {
            if (read_printer_data()) {
                retval = 1;
            }

        } else if (!strcmp(argv[i], "-write_data")) {
            if (write_printer_data()) {
                retval = 1;
            }

        } else if (!strcmp(argv[i], "-NB_read_data")) {
            if (read_NB_printer_data()) {
                retval = 1;
            }

        } else {
            usage(argv[i]);
            retval = 1;
        }
    }

    exit(retval);
}
```
