# 分配槽

Redis集群把所有的数据映射到16384个槽中。每个key会映射为一个固定的槽，只有当节点分配了槽，才能响应和这些槽关联的键命令。通过cluster addslots命令为节点分配槽。这里利用bash特性批量设置槽（slots）， 命令如下：

```text
redis-cli -h 127.0.0.1 -p 6379 cluster addslots {0...5461} 
redis-cli -h 127.0.0.1 -p 6380 cluster addslots {5462...10922} 
redis-cli -h 127.0.0.1 -p 6381 cluster addslots {10923...16383}
```

把16384个slot平均分配给6379、6380、6381三个节点。执行cluster info 查看集群状态，如下所示：

![](../../.gitbook/assets/image%20%2834%29.png)

当前集群状态是OK，集群进入在线状态。所有的槽都已经分配给节 点，执行cluster nodes命令可以看到节点和槽的分配关系：

```text
127.0.0.1:6379> cluster nodes
4fa7eac4080f0b667ffeab9b87841da49b84a6e4 127.0.0.1:6384 master - 0 1468076240123 5 connected 
cfb28ef1deee4e0fa78da86abe5d24566744411e 127.0.0.1:6379 myself,master - 0 0 0 connected 0-5461 
be9485a6a729fc98c5151374bc30277e89a461d8 127.0.0.1:6383 master - 0 1468076239622 4 connected 
40622f9e7adc8ebd77fca0de9edfe691cb8a74fb 127.0.0.1:6382 master - 0 1468076240628 3 connected 
8e41673d59c9568aa9d29fb174ce733345b3e8f1 127.0.0.1:6380 master - 0 1468076237606 1 connected
40b8d09d44294d2e23c7c768efc8fcd153446746 127.0.0.1:6381 master - 0 1468076238612 2 connected
```

目前还有三个节点没有使用，作为一个完整的集群，每个负责处理槽的 节点应该具有从节点，保证当它出现故障时可以自动进行故障转移。集群模 式下，Reids节点角色分为主节点和从节点。首次启动的节点和被分配槽的 节点都是主节点，从节点负责复制主节点槽信息和相关的数据。使用cluster replicate{nodeId}命令让一个节点成为从节点。其中命令执行必须在对应的 从节点上执行，nodeId是要复制主节点的节点ID，命令如下：

```text
127.0.0.1:6382>cluster replicate cfb28ef1deee4e0fa78da86abe5d24566744411e 
OK
127.0.0.1:6383>cluster replicate 8e41673d59c9568aa9d29fb174ce733345b3e8f1 
OK
127.0.0.1:6384>cluster replicate 40b8d09d44294d2e23c7c768efc8fcd153446746 
OK
```

Redis集群模式下的主从复制使用了之前介绍的Redis复制流程，依然支 持全量和部分复制。复制（replication）完成后，整个集群的结构如图所示。

![](../../.gitbook/assets/image%20%2810%29.png)

通过cluster nodes命令查看集群状态和复制关系，如下所示：

![](../../.gitbook/assets/image%20%2844%29.png)

目前为止，我们依照Redis协议手动建立一个集群。它由6个节点构成， 3个主节点负责处理槽和相关数据，3个从节点负责故障转移。手动搭建集群 便于理解集群建立的流程和细节，不过读者也从中发现集群搭建需要很多步 骤，当集群节点众多时，必然会加大搭建集群的复杂度和运维成本。因此 Redis官方提供了redis-trib.rb工具方便我们快速搭建集群。

