# 原理

在从节点执行slaveof命令后，复制过程便开始运作，下面详细介绍建立 复制的完整流程，如图所示。

![](../.gitbook/assets/image%20%2880%29.png)

从图中可以看出复制过程大致分为6个过程：

### 1）保存主节点（master）信息。

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

### 2）从节点逻辑

从节点（slave）内部通过每秒运行的定时任务维护复制相关逻辑， 当定时任务发现存在新的主节点后，会尝试与该节点建立网络连接，如图所示。

![](../.gitbook/assets/image%20%28128%29.png)

从节点会建立一个socket套接字，例如图6-8中从节点建立了一个端口为 24555的套接字，专门用于接受主节点发送的复制命令。从节点连接成功后打印如下日志：

```text
* Connecting to MASTER 127.0.0.1:6379
* MASTER <-> SLAVE sync started
```

如果从节点无法建立连接，定时任务会无限重试直到连接成功或者执行 slaveof no one取消复制，如图所示。

![](../.gitbook/assets/image%20%28229%29.png)

关于连接失败，可以在从节点执行info replication查看 master\_link\_down\_since\_seconds指标，它会记录与主节点连接失败的系统时间。从节点连接主节点失败时也会每秒打印如下日志，方便运维人员发现问 题：

```text
# Error condition on socket for SYNC: {socket_error_reason}
```

### 3）发送ping命令

连接建立成功后从节点发送ping请求进行首次通信，ping请求主要目的如下：

* 检测主从之间网络套接字是否可用
* 检测主节点当前是否可接受处理命令

如果发送ping命令后，从节点没有收到主节点的pong回复或者超时，比如网络超时或者主节点正在阻塞无法响应命令，从节点会断开复制连接，下次定时任务会发起重连，如图所示。

![](../.gitbook/assets/image%20%2864%29.png)

从节点发送的ping命令成功返回，Redis打印如下日志，并继续后续复制流程：

```text
Master replied to PING, replication can continue...
```

### 4）权限验证

如果主节点设置了requirepass参数，则需要密码验证， 从节点必须配置masterauth参数保证与主节点相同的密码才能通过验证；如 果验证失败复制将终止，从节点重新发起复制流程。

### 5）同步数据集

主从复制连接正常通信后，对于首次建立复制的场 景，主节点会把持有的数据全部发送给从节点，这部分操作是耗时最长的步 骤。Redis在2.8版本以后采用新复制命令psync进行数据同步，原来的sync命 令依然支持，保证新旧版本的兼容性。新版同步划分两种情况：全量同步和部分同步，下一节将重点介绍。

### 6）命令持续复制

当主节点把当前的数据同步给从节点后，便完成了 、复制的建立流程。接下来主节点会持续地把写命令发送给从节点，保证主从数据一致性。

## **redis replication 的核心机制**

* redis 采用**异步方式**复制数据到 slave 节点，不过 redis2.8 开始，slave node 会周期性地确认自己每次复制的数据量；
* 一个 master node 是可以配置多个 slave node 的；
* slave node 也可以连接其他的 slave node；
* slave node 做复制的时候，不会 block master node 的正常工作；
* slave node 在做复制的时候，也不会 block 对自己的查询操作，它会用旧的数据集来提供服务；但是复制完成的时候，需要删除旧数据集，加载新数据集，这个时候就会暂停对外服务了；
* slave node 主要用来进行横向扩容，做读写分离，扩容的 slave node 可以提高读的吞吐量。

注意，如果采用了主从架构，那么建议必须**开启** master node 的持久化，不建议用 slave node 作为 master node 的数据热备，因为那样的话，如果你关掉 master 的持久化，可能在 master 宕机重启的时候数据是空的，然后可能一经过复制， slave node 的数据也丢了。

另外，master 的各种备份方案，也需要做。万一本地的所有文件丢失了，从备份中挑选一份 rdb 去恢复 master，这样才能**确保启动的时候，是有数据的**，即使采用了后续讲解的高可用机制，slave node 可以自动接管 master node，但也可能 sentinel 还没检测到 master failure，master node 就自动重启了，还是可能导致上面所有的 slave node 数据被清空。

