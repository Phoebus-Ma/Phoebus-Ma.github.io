
# 1. EEPROM Application

at24_app.c for at24c02:

```c
#include <linux/i2c-dev.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdlib.h>
#include <stdio.h>
#include <sys/ioctl.h>
#include <fcntl.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>

#define I2C_SLAVE       0x0703

void print_usage(char *str)
{
    printf("%s r     : read at24c02 addresss 0\n", str);
    printf("%s w val : write at24c02 addresss 0\n", str);
}

int main(int argc,char **argv)
{
    int fd;
    unsigned char val;          //字节

    char register_addr = 0x08;  // Device register to access 片内地址
    int  res;
    char wbuf[10];
    char rbuf[10];

    if (argc < 2){
        print_usage(argv[0]);
        exit(1);
    }

    // 打开设备文件
    fd = open("/dev/at24_dev", O_RDWR);
    if (fd < 0) 
    {
        perror("open failed");
        exit(1);
    }

    if (strcmp(argv[1], "r") == 0){
        if (write(fd, &register_addr, 1)!=1) {
            perror("write failed");
            exit(1);
        }

        if (read(fd, rbuf, 1) != 1) {
            perror("read failed");
            exit(1);
        } else {
            printf("rbuf =0x%x\n",rbuf[0]);

        }	

    }
    else if ((strcmp(argv[1], "w") == 0) && (argc == 3))
    {
        //  ./test  w  0x99
        val = strtoul(argv[2], NULL, 0);

        wbuf[0] = register_addr; // 片内地址0x08
        wbuf[1] = val;

        if (write(fd, wbuf, 2)!=2) {
            perror("write failed");
            exit(1);
        }		      
        printf("write data ok!\n");

    }

    close(fd);

    return 0;
}
```


# 2. EEPROM Driver

at24_drv.c:

```c
#include <linux/init.h>
#include <linux/module.h>
#include <linux/platform_device.h>
#include <linux/fs.h>
#include <linux/slab.h>
#include <linux/cdev.h>
#include <linux/device.h>
#include <linux/interrupt.h>
#include <linux/input.h>
#include <linux/wait.h>
#include <linux/sched.h>
#include <linux/i2c.h>

#include <asm/uaccess.h>


struct i2c_e2prom{
    int major;
    struct device dev;
    struct i2c_client *client;
};


struct i2c_e2prom *at24_dev;

/*
1, 构建i2c driver ,注册到总线中
2, 如果匹配成功,会调用probe方法,实现probe方法
3, 在probe中需要做:
    a,与用户交互
        1,申请设备号
        2,创建设备节点
        3,硬件操作
        4,实现fops
*/

struct class at24_class = {
    .name   = "at24_cls",
};

// 实现两个函数,类似于i2c_master_recv/i2c_master_send
int at24_i2c_write(struct i2c_client *client, char *buf, int count)
{
    int ret = -1;
    struct i2c_adapter  *adapter = client->adapter;
    struct i2c_msg msg;

    msg.addr = client->addr;
    msg.flags = 0;
    msg.len = count;
    msg.buf = buf ;

    //调用i2c_transfer
    //参数1---i2c_adapter对象--来自于 i2c client
    //参数2--要发送的数据包
    //参数3--要发送msg的个数
    ret = i2c_transfer(adapter, &msg, 1);

    return ret==1?count:ret;
}

int at24_i2c_read(struct i2c_client *client, char *buf, int count)
{
    int ret = -1;
    struct i2c_adapter  *adapter = client->adapter;
    struct i2c_msg msg;

    msg.addr = client->addr;
    msg.flags = I2C_M_RD;
    msg.len = count;
    msg.buf = buf ;

    //调用i2c_transfer
    //参数1---i2c_adapter对象--来自于 i2c client
    //参数2--要发送的数据包
    //参数3--要发送msg的个数
    ret = i2c_transfer(adapter, &msg, 1);

    return ret==1?count:ret;
}

void at24_dev_release(struct device *dev)
{

}

// 4, 实现fops
int at24_drv_open(struct inode *inode, struct file *filp)
{
    return 0;
}

ssize_t at24_drv_read(struct file *filp, char __user *buf, size_t count, loff_t *fpos)
{
    int ret;

    if(count <=0 || count > 256)
        return -EINVAL;

    char *tmp_buf = kzalloc(count, GFP_KERNEL);

    // 1, 从硬件中获取到数据
    ret = at24_i2c_read(at24_dev->client,  tmp_buf, count);
    if(ret < 0)
    {
        printk("at24_i2c_read error\n");
        kfree(tmp_buf);
        return ret;
    }

    // 2, 将数据传递到用户空间
    ret = copy_to_user(buf,tmp_buf, count);
    if(ret > 0)
    {
        printk("copy_to_user error\n");
        kfree(tmp_buf);
        return -EFAULT;
    }

    kfree(tmp_buf);

    return count;
}

ssize_t at24_drv_write(struct file *filp, const char __user *buf, size_t count, loff_t *fpos)
{
    int ret;

    if(count <=0 || count > 256)
        return -EINVAL;

    char *tmp_buf = kzalloc(count, GFP_KERNEL);

    // 1, 从用户空间获取数据
    ret = copy_from_user(tmp_buf, buf, count);
    if(ret > 0)
    {
        printk("copy_from_user error\n");
        kfree(tmp_buf);
        return -EFAULT;
    }

    // 2, 把数据写入到硬件中
    ret = at24_i2c_write(at24_dev->client, tmp_buf, count);
    if(ret < 0)
    {
        printk("at24_i2c_read error\n");
        kfree(tmp_buf);
        return ret;
    }

    kfree(tmp_buf);

    return count;
}

int at24_drv_close (struct inode *inode, struct file *filp)
{
    return 0;
}

const struct file_operations  at24_fops = {
    .open    = at24_drv_open,
    .read    = at24_drv_read,
    .write   = at24_drv_write,
    .release = at24_drv_close,
};

int at24_drv_probe(struct i2c_client *client, const struct i2c_device_id *id)
{
    printk("id->name = %s, id->driver_data = 0x%x\n", id->name, id->driver_data);

    // 0--分配一个全局的设备对象
    at24_dev = kzalloc(sizeof(struct i2c_e2prom), GFP_KERNEL);

    // 1,申请设备号
    at24_dev->major = register_chrdev(0, "at24_drv", &at24_fops);

    // 2, 创建设备节点class_create, device_create
    class_register(&at24_class);

    dev_set_name(&at24_dev->dev, "at24_dev");
    at24_dev->dev.devt = MKDEV(at24_dev->major,  0);
    at24_dev->dev.class = &at24_class;
    at24_dev->dev.parent = NULL;
    at24_dev->dev.release = at24_dev_release;
    device_initialize(&at24_dev->dev);
    device_add(&at24_dev->dev);
    // 3, 硬件初始化--- at24c02上电就可以使用,所以不需要初始化

    // 5, 记录当前的i2c client对象
    at24_dev->client = client;

    return 0;
}

int at24_drv_remove(struct i2c_client *client)
{
    device_destroy(&at24_class, MKDEV(at24_dev->major,  0));
    class_destroy(&at24_class);
    unregister_chrdev(at24_dev->major, "at24_drv");
    kfree(at24_dev);

    return 0;
}

const struct i2c_device_id at24_id_table[] = {
    {"at24c02", 0x2},
    {"at24c04", 0x4},
    {"at24c08", 0x8},
};

struct i2c_driver at24_drv = {
    .probe = at24_drv_probe,
    .remove = at24_drv_remove,
    .driver = {             // 必须初始化
        .name = "at24_drv", // 不用做匹配, /sys/bus/drivers/at24_drv
    },
    .id_table = at24_id_table,
};

static int __init at24_drv_init(void)
{
    // 1, 构建i2c driver ,注册到总线中
    return i2c_add_driver(&at24_drv);
}

static void __exit at24_drv_exit(void)
{
    i2c_del_driver(&at24_drv);
}

module_init(at24_drv_init);
module_exit(at24_drv_exit);
MODULE_LICENSE("GPL");
```


# 3. build

```bash
#指定内核源码路径
KERNEL_DIR = /home/farsight/fs210/kernel/linux-3.0.8

#指定当前路径
CUR_DIR = $(shell pwd)

MYAPP = at24_app
MODULE = at24_drv

all:
	make -C $(KERNEL_DIR) M=$(CUR_DIR) modules
	arm-none-linux-gnueabi-gcc -o $(MYAPP) $(MYAPP).c
clean:
	make -C $(KERNEL_DIR) M=$(CUR_DIR) clean
	$(RM) $(MYAPP)
install:
	cp -raf *.ko $(MYAPP) /opt/rootfs/drv_module

#指定编译当前目录下哪个源文件
obj-m = $(MODULE).o
```
