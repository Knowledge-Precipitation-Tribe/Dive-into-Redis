# 原理

在从节点执行slaveof命令后，复制过程便开始运作，下面详细介绍建立 复制的完整流程，如图所示。

![](../.gitbook/assets/image%20%2835%29.png)

从图中可以看出复制过程大致分为6个过程：

### 1\) 保存主节点（master）信息。

执行slaveof后从节点只保存主节点的地址信息便直接返回，这时建立复制流程还没有开始，在从节点6380执行info replication可以看到如下信息：

```text
master_host:127.0.0.1
master_port:6379
master_link_status:down
```

从统计信息可以看出，主节点的ip和port被保存下来，但是主节点的连接状态（master\_link\_status）是下线状态。执行slaveof后Redis会打印如下日志：

```text
SLAVE OF 127.0.0.1:6379 enabled (user request from 'id=65 addr=127.0.0.1:58090
fd=5 name= age=11 idle=0 flags=N db=0 sub=0 psub=0 multi=-1 qbuf=0 qbuf-free= 32768 obl=0 oll=0 omem=0 events=r cmd=slaveof')
```

通过该日志可以帮助运维人员定位发送slaveof命令的客户端，方便追踪和发现问题。

