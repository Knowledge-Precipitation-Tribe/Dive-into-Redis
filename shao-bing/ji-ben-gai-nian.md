# 基本概念

Redis的主从复制模式下，一旦主节点由于故障不能提供服务，需要人工将从节点晋升为主节点，同时还要通知应用方更新主节点地址，对于很 应用场景这种故障处理的方式是无法接受的。可喜的是Redis从2.8开始正式提供了Redis Sentinel（哨兵）架构来解决这个问题，本章会对Redis Sentinel 进行详细分析，相信通过本章的学习，读者完全可以在自己的项目中合理地使用和运维Redis Sentinel。

由于对Redis的许多概念都有不同的名词解释，所以在介绍Redis Sentinel之前，先对几个名词进行说明，这样便于在后面的介绍中达成一致，如表所示。

![](../.gitbook/assets/image%20%28136%29.png)

Redis Sentinel是Redis的高可用实现方案，在实际的生产环境中，对提高整个系统的高可用性是非常有帮助的，本节首先会回顾主从复制模式下故障处理可能产生的问题，而后引出高可用的概念，最后重点分析Redis Sentinel的基本架构、优势，以及是如何实现高可用的。

## 主从复制的问题

Redis的主从复制模式可以将主节点的数据改变同步给从节点，这样从节点就可以起到两个作用：第一，作为主节点的一个备份，一旦主节点出了故障不可达的情况，从节点可以作为后备“顶”上来，并且保证数据尽量不丢失（主从复制是最终一致性）。第二，从节点可以扩展主节点的读能力，一 旦主节点不能支撑住大并发量的读操作，从节点可以在一定程度上帮助主节点分担读压力。

但是主从复制也带来了以下问题：

* 一旦主节点出现故障，需要手动将一个从节点晋升为主节点，同时需要修改应用方的主节点地址，还需要命令其他从节点去复制新的主节点，整 个过程都需要人工干预。
* 主节点的写能力受到单机的限制。
* 主节点的存储能力受到单机的限制。

其中第一个问题就是Redis的高可用问题，将在下一个小节进行分析。 第二、三个问题属于Redis的分布式问题，会在集群部分介绍。

## 高可用

Redis主从复制模式下，一旦主节点出现了故障不可达，需要人工干预进行故障转移，无论对于Redis的应用方还是运维方都带来了很大的不便。 对于应用方来说无法及时感知到主节点的变化，必然会造成一定的写数据丢失和读数据错误，甚至可能造成应用方服务不可用。对于Redis的运维方来说，整个故障转移的过程是需要人工来介入的，故障转移实时性和准确性上都无法得到保障，下图展示了一个1主2从的Redis主从复制模式下的主节点出现故障后，是如何进行故障转移的，过程如下所示。

1）主节点发生故障后，客户端（client）连接主节点失败，两个从节点与主节点连接失败造成复制中断。

![](../.gitbook/assets/image%20%28167%29.png)

2）如果主节点无法正常启动，需要选出一个从节点 （slave-1），对其执行slaveof no one命令使其成为新的主节点。

![](../.gitbook/assets/image%20%28200%29.png)

3）原来的从节点（slave-1）成为新的主节点后，更新应用方的主节点信息，重新启动应用方。

![](../.gitbook/assets/image%20%28114%29.png)

4）客户端命令另一个从节点（slave-2）去复制新的主节点（new-master）

![](../.gitbook/assets/image%20%28161%29.png)

5）待原来的主节点恢复后，让它去复制新的主节点。

![](../.gitbook/assets/image%20%28132%29.png)

上述处理过程就可以认为整个服务或者架构的设计不是高可用的，因为整个故障转移的过程需要人介入。考虑到这点，有些公司把上述流程自动化 了，但是仍然存在如下问题：第一，判断节点不可达的机制是否健全和标准。第二，如果有多个从节点，怎样保证只有一个被晋升为主节点。第三， 通知客户端新的主节点机制是否足够健壮。Redis Sentinel正是用于解决这些问题。

## Redis Sentinel的高可用性

当主节点出现故障时，Redis Sentinel能自动完成故障发现和故障转移， 并通知应用方，从而实现真正的高可用。

Redis2.6版本提供Redis Sentinel v1版本，但是功能性和健壮性都有一些问题，如果想使用Redis Sentinel的话，建议使用2.8以上版本，也就是v2版本的Redis Sentinel。

Redis Sentinel是一个分布式架构，其中包含若干个Sentinel节点和Redis数据节点，每个Sentinel节点会对数据节点和其余Sentinel节点进行监控，当它发现节点不可达时，会对节点做下线标识。如果被标识的是主节点，它还会和其他Sentinel节点进行“协商”，当大多数Sentinel节点都认为主节点不可达时，它们会选举出一个Sentinel节点来完成自动故障转移的工作，同时会将这个变化实时通知给Redis应用方。整个过程完全是自动的，不需要人工来介入，所以这套方案很有效地解决了Redis的高可用问题。

这里的分布式是指：Redis数据节点、Sentinel节点集合、客户端分布在多个物理节点的架构，不要与集群操作中的的Redis Cluster分布式混淆。

如图所示，Redis Sentinel与Redis主从复制模式只是多了若干Sentinel 节点，所以Redis Sentinel并没有针对Redis节点做了特殊处理，这里是很多开发和运维人员容易混淆的。

![](../.gitbook/assets/image%20%28174%29.png)

从逻辑架构上看，Sentinel节点集合会定期对所有节点进行监控，特别是对主节点的故障实现自动转移。

下面以1个主节点、2个从节点、3个Sentinel节点组成的Redis Sentinel为例子进行说明，拓扑结构如图所示。

![](../.gitbook/assets/image%20%28135%29.png)

整个故障转移的处理逻辑有下面4个步骤：

1）如图所示，主节点出现故障，此时两个从节点与主节点失去连接，主从复制失败。

![](../.gitbook/assets/image%20%2814%29.png)

2）每个Sentinel节点通过定期监控发现主节点出现了故障。

![](../.gitbook/assets/image%20%2878%29.png)

3）多个Sentinel节点对主节点的故障达成一致，选举出 sentinel-3节点作为领导者负责故障转移。

![](../.gitbook/assets/image%20%28222%29.png)

4）Sentinel领导者节点执行了故障转移，整个过程和高可用中介绍的是完全一致的，只不过是自动化完成的。

![](../.gitbook/assets/image%20%2883%29.png)

5）故障转移后整个Redis Sentinel的拓扑结构图所示。

![](../.gitbook/assets/image%20%2818%29.png)

通过上面介绍的Redis Sentinel逻辑架构以及故障转移的处理，可以看出Redis Sentinel具有以下几个功能： 

* **监控：**Sentinel节点会定期检测Redis数据节点、其余Sentinel节点是否可达。 
* **通知：**Sentinel节点会将故障转移的结果通知给应用方。 
* **主节点故障转移：**实现从节点晋升为主节点并维护后续正确的主从关系。 
* **配置提供者：**在Redis Sentinel结构中，客户端在初始化的时候连接的是Sentinel节点集合，从中获取主节点信息。

同时看到，Redis Sentinel包含了若个Sentinel节点，这样做也带来了两个好处：

* 对于节点的故障判断是由多个Sentinel节点共同完成，这样可以有效地防止误判。
* Sentinel节点集合是由若干个Sentinel节点组成的，这样即使个别Sentinel节点不可用，整个Sentinel节点集合依然是健壮的。

但是Sentinel节点本身就是独立的Redis节点，只不过它们有一些特殊， 它们不存储数据，只支持部分命令。下一节将完整介绍Redis Sentinel的部署过程，相信在安装和部署完Redis Sentinel后，读者能更清晰地了解Redis Sentinel的整体架构。
