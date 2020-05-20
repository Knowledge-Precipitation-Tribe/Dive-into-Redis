# 节点选择

虽然Gossip协议的信息交换机制具有天然的分布式特性，但它是有成本 的。由于内部需要频繁地进行节点信息交换，而ping/pong消息会携带当前节 点和部分其他节点的状态数据，势必会加重带宽和计算的负担。Redis集群 内节点通信采用固定频率（定时任务每秒执行10次）。因此节点每次选择需 要通信的节点列表变得非常重要。通信节点选择过多虽然可以做到信息及时 交换但成本过高。节点选择过少会降低集群内所有节点彼此信息交换频率， 从而影响故障判定、新节点发现等需求的速度。因此Redis集群的Gossip协 议需要兼顾信息交换实时性和成本开销，通信节点选择的规则如图所示。

![](../../.gitbook/assets/image%20%284%29.png)

根据通信节点选择的流程可以看出消息交换的成本主要体现在单位时间 选择发送消息的节点数量和每个消息携带的数据量。

## 1.选择发送消息的节点数量

集群内每个节点维护定时任务默认每秒执行10次，每秒会随机选取5个 节点找出最久没有通信的节点发送ping消息，用于保证Gossip信息交换的随 机性。每100毫秒都会扫描本地节点列表，如果发现节点最近一次接受pong 消息的时间大于cluster\_node\_timeout/2，则立刻发送ping消息，防止该节点信 息太长时间未更新。根据以上规则得出每个节点每秒需要发送ping消息的数 量=1+10\*num（node.pong\_received&gt;cluster\_node\_timeout/2），因此 cluster\_node\_timeout参数对消息发送的节点数量影响非常大。当我们的带宽 资源紧张时，可以适当调大这个参数，如从默认15秒改为30秒来降低带宽占 用率。过度调大cluster\_node\_timeout会影响消息交换的频率从而影响故障转 移、槽信息更新、新节点发现的速度。因此需要根据业务容忍度和资源消耗 进行平衡。同时整个集群消息总交换量也跟节点数成正比。

## 2.消息数据量

每个ping消息的数据量体现在消息头和消息体中，其中消息头主要占用 空间的字段是myslots\[CLUSTER\_SLOTS/8\]，占用2KB，这块空间占用相对 固定。消息体会携带一定数量的其他节点信息用于信息交换。具体数量见以 下伪代码：

```text
def get_wanted():
    int total_size = size(cluster.nodes) 
    # 默认包含节点总量的1/10
    int wanted = floor(total_size/10); 
    if wanted < 3:
        # 至少携带3个其他节点信息 
        wanted = 3; 
    if wanted > total_size -2 :
        # 最多包含total_size - 2个 
        wanted = total_size - 2; 
    return wanted;
```

根据伪代码可以看出消息体携带数据量跟集群的节点数息息相关，更大 的集群每次消息通信的成本也就更高，因此对于Redis集群来说并不是大而 全的集群更好，对于集群规模控制的建议见之后“集群运维”。
