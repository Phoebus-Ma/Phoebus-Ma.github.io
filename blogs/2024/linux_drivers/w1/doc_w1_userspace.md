
# Userspace communication protocol over connector

# 1. Message types

w1核心和用户空间之间的消息分为三种类型：

1. 事件。 每次由于自动或请求的搜索而找到新的主设备或从设备时，都会生成它们。
2. 用户空间命令。
3. 回复用户空间命令。


# 2. Protocol

```bash
[struct cn_msg] - connector header.
      Its length field is equal to size of the attached data
[struct w1_netlink_msg] - w1 netlink header.
      __u8 type       - message type.
                      W1_LIST_MASTERS
                              list current bus masters
                      W1_SLAVE_ADD/W1_SLAVE_REMOVE
                              slave add/remove events
                      W1_MASTER_ADD/W1_MASTER_REMOVE
                              master add/remove events
                      W1_MASTER_CMD
                              userspace command for bus master
                              device (search/alarm search)
                      W1_SLAVE_CMD
                              userspace command for slave device
                              (read/write/touch)
      __u8 status     - error indication from kernel
      __u16 len       - size of data attached to this header data
      union {
              __u8 id[8];                      - slave unique device id
              struct w1_mst {
                      __u32           id;      - master's id
                      __u32           res;     - reserved
              } mst;
      } id;

[struct w1_netlink_cmd] - command for given master or slave device.
      __u8 cmd        - command opcode.
                      W1_CMD_READ     - read command
                      W1_CMD_WRITE    - write command
                      W1_CMD_SEARCH   - search command
                      W1_CMD_ALARM_SEARCH - alarm search command
                      W1_CMD_TOUCH    - touch command
                              (write and sample data back to userspace)
                      W1_CMD_RESET    - send bus reset
                      W1_CMD_SLAVE_ADD        - add slave to kernel list
                      W1_CMD_SLAVE_REMOVE     - remove slave from kernel list
                      W1_CMD_LIST_SLAVES      - get slaves list from kernel
      __u8 res        - reserved
      __u16 len       - length of data for this command
              For read command data must be allocated like for write command
      __u8 data[0]    - data for this command
```

每个连接器消息可以包括一个或多个 w1_netlink_msg 以及零个或多个附加的 w1_netlink_cmd 消息。

对于事件消息，没有 w1_netlink_cmd 嵌入结构，只有连接器标头和 w1_netlink_msg 结构，其中“len”字段为零并填充类型（事件类型之一）和 id：主机顺序中的 8 个字节的从站唯一 id，或主站的 id， 当它被添加到w1核心时被分配给总线主设备。

当前仅针对读取命令请求生成对用户空间命令的回复。 为一个 w1_netlink_cmd 读取请求准确生成一个回复。 发送时回复不会合并 - 即典型的回复消息如下所示：

```bash
[cn_msg][w1_netlink_msg][w1_netlink_cmd]
cn_msg.len = sizeof(struct w1_netlink_msg) +
           sizeof(struct w1_netlink_cmd) +
           cmd->len;
w1_netlink_msg.len = sizeof(struct w1_netlink_cmd) + cmd->len;
w1_netlink_cmd.len = cmd->len;
```

对 W1_LIST_MASTERS 的回复应向用户空间发送一条消息，其中包含所有已注册主 ID 的列表，格式如下：

```bash
cn_msg (CN_W1_IDX.CN_W1_VAL as id, len is equal to sizeof(struct
w1_netlink_msg) plus number of masters multiplied by 4)
w1_netlink_msg (type: W1_LIST_MASTERS, len is equal to
        number of masters multiplied by 4 (u32 size))
id0 ... idN
```

每条消息的大小最多为 4k，因此如果主设备数量超过此大小，则会将其拆分为多条消息。

W1搜索和报警搜索命令。

请求:

```bash
[cn_msg]
  [w1_netlink_msg type = W1_MASTER_CMD
      id is equal to the bus master id to use for searching]
  [w1_netlink_cmd cmd = W1_CMD_SEARCH or W1_CMD_ALARM_SEARCH]
```

回复:

```bash
[cn_msg, ack = 1 and increasing, 0 means the last message,
      seq is equal to the request seq]
[w1_netlink_msg type = W1_MASTER_CMD]
[w1_netlink_cmd cmd = W1_CMD_SEARCH or W1_CMD_ALARM_SEARCH
      len is equal to number of IDs multiplied by 8]
[64bit-id0 ... 64bit-idN]
```

每个标头中的长度对应于其后面数据的大小，因此 w1_netlink_cmd->len = N * 8； 其中 N 是该消息中的 ID 数量。 可以为零。

```bash
w1_netlink_msg->len = sizeof(struct w1_netlink_cmd) + N * 8;
cn_msg->len = sizeof(struct w1_netlink_msg) +
            sizeof(struct w1_netlink_cmd) +
            N*8;
```

W1复位命令：

```bash
[cn_msg]
  [w1_netlink_msg type = W1_MASTER_CMD
      id is equal to the bus master id to use for searching]
  [w1_netlink_cmd cmd = W1_CMD_RESET]
```


# 3. Command status replies

每个命令（无论是根命令、主命令还是从命令，无论是否有 w1_netlink_cmd 结构）都将被 w1 核心“确认”。 回复的格式与请求消息相同，只是长度参数不考虑用户请求的数据，即读/写/触摸 IO 请求不会包含数据，因此 w1_netlink_cmd.len 将为 0，w1_netlink_msg.len 将为 w1_netlink_cmd 结构和 cn_msg.len 的大小将等于 sizeof(struct w1_netlink_msg) 和 sizeof(struct w1_netlink_cmd) 之和。 如果为 master 或 root 命令（未附加 w1_netlink_cmd）生成回复，则回复将仅包含 cn_msg 和 w1_netlink_msg 结构。

w1_netlink_msg.status 字段将携带正错误值（例如 EINVAL）或零（如果成功）。

每个结构中的所有其他字段将反映请求消息中的相同参数（除了上述长度）。

为 w1_netlink_msg 中嵌入的每个 w1_netlink_cmd 生成状态回复，如果没有 w1_netlink_cmd 结构，将为 w1_netlink_msg 生成回复。

所有w1_netlink_cmd命令结构都会在每个w1_netlink_msg中处理，即使有错误，也只有长度不匹配才会中断消息处理。


# 4. Operation steps in w1 core when new command is received

当收到新消息（w1_netlink_msg）时，w1 核心根据 w1_netlink_msg.type 字段检测它是主请求还是从请求。 然后搜索主设备或从设备。 找到后，主设备（请求的设备或找到从设备的设备）将被锁定。 如果请求从机命令，则启动重置/选择过程以选择给定设备。

然后w1_netlink_msg中请求的所有操作都一一执行。 如果命令需要回复（如读取命令），则会在命令完成时发送。

处理完所有命令 (w1_netlink_cmd) 后，主设备将解锁，并开始下一个 w1_netlink_msg 标头处理。


# 5. Connector [1] specific documentation

每个连接器消息都包含两个 u32 字段作为“地址”。 w1 使用 include/linux/connector.h 标头中定义的 CN_W1_IDX 和 CN_W1_VAL 。 每条消息还包括序列号和确认号。 事件消息的序列号是适当的总线主控序列号，随着“通过”该主控发送的每个事件消息而增加。 用户空间请求的序列号由用户空间应用程序设置。 回复的序列号与请求中的序列号相同，确认号设置为 seq+1。


# 6. Additional documentation, source code examples

[Kernel Connector](https://www.kernel.org/doc/html/latest/driver-api/connector.html)

<http://www.ioremap.net/archive/w1>

该存档包括用户空间应用程序 w1d.c，它对总线上找到的所有主/从设备使用读/写/搜索命令。
