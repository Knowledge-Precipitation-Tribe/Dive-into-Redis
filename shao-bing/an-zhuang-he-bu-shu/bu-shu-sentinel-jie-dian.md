# 部署Sentinel节点

3个Sentinel节点的部署方法是完全一致的（端口不同），下面以 sentinel-1节点的部署为例子进行说明。

## 配置Sentinel节点

{% code title="redis-sentinel-26379.conf" %}
```text
port 26379
daemonize yes logfile "26379.log"
dir /opt/soft/redis/data
sentinel monitor mymaster 127.0.0.1 6379 2
sentinel down-after-milliseconds mymaster 30000
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 180000
```
{% endcode %}

1）Sentinel节点的默认端口是26379。

2）sentinel monitor mymaster127.0.0.1 6379 2配置代表sentinel-1节点需要监控127.0.0.1:6379这个主节点，2代表判断主节点失败至少需要2个Sentinel节点同意，mymaster是主节点的别名，其余Sentinel配置将在下一节进行详细说明。

## 启动Sentinel节点

Sentinel节点的启动方法有两种：

方法一，使用redis-sentinel命令：

```text
redis-sentinel redis-sentinel-26379.conf
```

方法二，使用redis-server命令加--sentinel参数：

```text
redis-server redis-sentinel-26379.conf --sentinel
```

两种方法本质上是一样的。

## 确认

Sentinel节点本质上是一个特殊的Redis节点，所以也可以通过info命令来查询它的相关信息，从下面info的Sentinel片段来看，Sentinel节点找到了主节点127.0.0.1:6379，发现了它的两个从节点，同时发现Redis Sentinel一共有3个Sentinel节点。这里只需要了解Sentinel节点能够彼此感知到对方，同时能够感知到Redis数据节点就可以了：

```text
$ redis-cli -h 127.0.0.1 -p 26379 info Sentinel
# Sentinel
sentinel_masters:1
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
master0:name=mymaster,status=ok,address=127.0.0.1:6379,slaves=2,sentinels=3
```

当三个Sentinel节点都启动后，整个拓扑结构如图所示。

![](../../.gitbook/assets/image%20%2860%29.png)

至此Redis Sentinel已经搭建起来了，整体上还是比较容易的，但是有2点需要强调一下：

1）生产环境中建议Redis Sentinel的所有节点应该分布在不同的物理机上。

2）Redis Sentinel中的数据节点和普通的Redis数据节点在配置上没有任何区别，只不过是添加了一些Sentinel节点对它们进行监控。

