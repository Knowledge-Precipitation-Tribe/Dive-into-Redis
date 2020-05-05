# 搭建集群

介绍完Redis集群分区规则之后，下面我们开始搭建Redis集群。搭建集 群工作需要以下三个步骤： 

1）准备节点。 

2）节点握手。 

3）分配槽。

## 准备节点

Redis集群一般由多个节点组成，节点数量至少为6个才能保证组成完整 高可用的集群。每个节点需要开启配置cluster-enabled yes，让Redis运行在集 群模式下。建议为集群内所有节点统一目录，一般划分三个目录：conf、 data、log，分别存放配置、数据和日志相关文件。把6个节点配置统一放在 conf目录下，集群相关配置如下：

```text
#节点端口 
port 6379
# 开启集群模式 
cluster-enabled yes
# 节点超时时间，单位毫秒 
cluster-node-timeout 15000
# 集群内部配置文件 
cluster-config-file "nodes-6379.conf"
```

其他配置和单机模式一致即可，配置文件命名规则redis-{port}.conf，准 备好配置后启动所有节点，命令如下：

```text
redis-server conf/redis-6379.conf
redis-server conf/redis-6380.conf
redis-server conf/redis-6381.conf
redis-server conf/redis-6382.conf
redis-server conf/redis-6383.conf
redis-server conf/redis-6384.conf
```

检查节点日志是否正确，日志内容如下：

```text
cat log/redis-6379.log
* No cluster configuration found, I'm cfb28ef1deee4e0fa78da86abe5d24566744411e
# Server started, Redis version 3.0.7
* The server is now ready to accept connections on port 6379
```

6379节点启动成功，第一次启动时如果没有集群配置文件，它会自动创建一份，文件名称采用cluster-config-file参数项控制，建议采用node{port}.conf格式定义，通过使用端口号区分不同节点，防止同一机器下多个 节点彼此覆盖，造成集群信息异常。如果启动时存在集群配置文件，节点会使用配置文件内容初始化集群信息。启动过程如图所示。

![](../../.gitbook/assets/image%20%2863%29.png)

集群模式的Redis除了原有的配置文件之外又加了一份集群配置文件。 当集群内节点信息发生变化，如添加节点、节点下线、故障转移等。节点会 自动保存集群状态到配置文件中。需要注意的是，Redis自动维护集群配置 文件，不要手动修改，防止节点重启时产生集群信息错乱。

如节点6379首次启动后生成集群配置如下：

```text
#cat data/nodes-6379.conf
cfb28ef1deee4e0fa78da86abe5d24566744411e 127.0.0.1:6379 myself,master - 0 0 0 connected vars currentEpoch 0 lastVoteEpoch 0
```

文件内容记录了集群初始状态，这里最重要的是节点ID，它是一个40位 16进制字符串，用于唯一标识集群内一个节点，之后很多集群操作都要借助 于节点ID来完成。需要注意是，节点ID不同于运行ID。节点ID在集群初始化 时只创建一次，节点重启时会加载集群配置文件进行重用，而Redis的运行 ID每次重启都会变化。在节点6380执行cluster nodes命令获取集群节点状态：

```text
127.0.0.1:6380>cluster nodes
8e41673d59c9568aa9d29fb174ce733345b3e8f1 127.0.0.1:6380 myself,master - 0 0 0 connected
```

每个节点目前只能识别出自己的节点信息。我们启动6个节点，但每个节点彼此并不知道对方的存在，下面通过节点握手让6个节点彼此建立联系从而组成一个集群。

