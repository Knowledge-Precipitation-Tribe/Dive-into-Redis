# 数据分布理论

分布式数据库首先要解决把整个数据集按照分区规则映射到多个节点的问题，即把数据集划分到多个节点上，每个节点负责整体数据的一个子集。 如图所示。

![](../../.gitbook/assets/image%20%2866%29.png)

需要重点关注的是数据分区规则。常见的分区规则有哈希分区和顺序分区两种，下表对这两种分区规则进行了对比。

![](../../.gitbook/assets/image%20%28102%29.png)

由于Redis Cluster采用哈希分区规则，这里我们重点讨论哈希分区，常 见的哈希分区规则有几种，下面分别介绍。

## 1.节点取余分区

使用特定的数据，如Redis的键或用户ID，再根据节点数量N使用公式： hash（key）%N计算出哈希值，用来决定数据映射到哪一个节点上。这种方 案存在一个问题：当节点数量变化时，如扩容或收缩节点，数据节点映射关 系需要重新计算，会导致数据的重新迁移。

这种方式的突出优点是简单性，常用于数据库的分库分表规则，一般采 用预分区的方式，提前根据数据量规划好分区数，比如划分为512或1024张 表，保证可支撑未来一段时间的数据量，再根据负载情况将表迁移到其他数 据库中。扩容时通常采用翻倍扩容，避免数据映射全部被打乱导致全量迁移 的情况，如图所示。

![](../../.gitbook/assets/image%20%28125%29.png)

## 2.一致性哈希分区

一致性哈希分区（Distributed Hash Table）实现思路是为系统中每个节点分配一个token，范围一般在0~2 32 ，这些token构成一个哈希环。数据读写 执行节点查找操作时，先根据key计算hash值，然后顺时针找到第一个大于 等于该哈希值的token节点，如图所示。

![](../../.gitbook/assets/image%20%2830%29.png)

这种方式相比节点取余最大的好处在于加入和删除节点只影响哈希环中 相邻的节点，对其他节点无影响。但一致性哈希分区存在几个问题：

* 加减节点会造成哈希环中部分数据无法命中，需要手动处理或者忽略 这部分数据，因此一致性哈希常用于缓存场景。
* 当使用少量节点时，节点变化将大范围影响哈希环中数据映射，因此 这种方式不适合少量数据节点的分布式方案。
* 普通的一致性哈希分区在增减节点时需要增加一倍或减去一半节点才 能保证数据和负载的均衡。

正因为一致性哈希分区的这些缺点，一些分布式系统采用虚拟槽对一致性哈希进行改进，比如Dynamo系统。

## 3.虚拟槽分区

虚拟槽分区巧妙地使用了哈希空间，使用分散度良好的哈希函数把所有数据映射到一个固定范围的整数集合中，整数定义为槽（slot）。这个范围一般远远大于节点数，比如Redis Cluster槽范围是0~16383。槽是集群内数据管理和迁移的基本单位。采用大范围槽的主要目的是为了方便数据拆分和集群扩展。每个节点会负责一定数量的槽，如图所示。

![](../../.gitbook/assets/image%20%28168%29.png)

当前集群有5个节点，每个节点平均大约负责3276个槽。由于采用高质量的哈希算法，每个槽所映射的数据通常比较均匀，将数据平均划分到5个节点进行数据分区。Redis Cluster就是采用虚拟槽分区，下面就介绍Redis数据分区方法。

