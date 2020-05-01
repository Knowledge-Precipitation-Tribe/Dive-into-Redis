# 节点握手

节点握手是指一批运行在集群模式下的节点通过Gossip协议彼此通信， 达到感知对方的过程。节点握手是集群彼此通信的第一步，由客户端发起命令：cluster meet{ip}{port}，如图所示。

![](../../.gitbook/assets/image%20%28212%29.png)

图中执行的命令是：cluster meet 127.0.0.1 6380让节点6379和6380节点进行握手通信。cluster meet命令是一个异步命令，执行之后立刻返回。内部发起与目标节点进行握手通信，如图所示。

![](../../.gitbook/assets/image%20%28149%29.png)

1）节点6379本地创建6380节点信息对象，并发送meet消息。

2）节点6380接受到meet消息后，保存6379节点信息并回复pong消息。

3）之后节点6379和6380彼此定期通过ping/pong消息进行正常的节点通信。

这里的meet、ping、pong消息是Gossip协议通信的载体，之后的节点通信部分做进一步介绍，它的主要作用是节点彼此交换状态数据信息。6379和 6380节点通过meet命令彼此建立通信之后，集群结构如图所示。

![](../../.gitbook/assets/image%20%2890%29.png)

对节点6379和6380分别执行cluster nodes命令，可以看到它们彼此已经感知到对方的存在。

![](../../.gitbook/assets/image%20%28219%29.png)

下面分别执行meet命令让其他节点加入到集群中：

```text
127.0.0.1:6379>cluster meet 127.0.0.1 6381
127.0.0.1:6379>cluster meet 127.0.0.1 6382
127.0.0.1:6379>cluster meet 127.0.0.1 6383
127.0.0.1:6379>cluster meet 127.0.0.1 6384
```

我们只需要在集群内任意节点上执行cluster meet命令加入新节点，握手 状态会通过消息在集群内传播，这样其他节点会自动发现新节点并发起握手 流程。最后执行cluster nodes命令确认6个节点都彼此感知并组成集群：

![](../../.gitbook/assets/image%20%28221%29.png)

节点建立握手之后集群还不能正常工作，这时集群处于下线状态，所有 的数据读写都被禁止。通过如下命令可以看到：

```text
127.0.0.1:6379> set hello redis
(error) CLUSTERDOWN The cluster is down
```

通过cluster info命令可以获取集群当前状态：

```text
127.0.0.1:6379> cluster info
cluster_state:fail
cluster_slots_assigned:0
cluster_slots_ok:0
cluster_slots_pfail:0 
cluster_slots_fail:0 
cluster_known_nodes:6 
cluster_size:0
...
```

从输出内容可以看到，被分配的槽（cluster\_slots\_assigned）是0，由于 目前所有的槽没有分配到节点，因此集群无法完成槽到节点的映射。只有当 16384个槽全部分配给节点后，集群才进入在线状态。

