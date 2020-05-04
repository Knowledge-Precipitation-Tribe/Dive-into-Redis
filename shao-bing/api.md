# API

Sentinel节点是一个特殊的Redis节点，它有自己专属的API，本节将对其进行介绍。为了方便演示，按下图进行说明：Sentinel节点集合监控着两组主从模式的Redis数据节点。

![](../.gitbook/assets/image%20%28191%29.png)

## 1.sentinel masters

展示所有被监控的主节点状态以及相关的统计信息，例如：

```text
127.0.0.1:26379> sentinel masters
1) 1) "name"
   2) "mymaster-2"
   3) "ip"
   4) "127.0.0.1"
   5) "port"
   6) "6382"
.........忽略............
2) 1) "name"
   2) "mymaster-1"
   3) "ip"
   4) "127.0.0.1"
   5) "port"
   6) "6379"
.........忽略............
```

## 2.sentinel master

展示指定的主节点状态以及相关的统计信息，例如：

```text
127.0.0.1:26379> sentinel master mymaster-1
1) "name"
2) "mymaster-1"
3) "ip"
4) "127.0.0.1"
5) "port"
6) "6379"
.........忽略............
```

## 3.sentinel slaves

展示指定的从节点状态以及相关的统计信息，例如：

```text
127.0.0.1:26379> sentinel slaves mymaster-1
1) 1) "name"
   2) "127.0.0.1:6380"
   3) "ip"
   4) "127.0.0.1"
   5) "port"
   6) "6380"
.........忽略............
2) 1) "name"
   2) "127.0.0.1:6381"
   3) "ip"
   4) "127.0.0.1"
   5) "port"
   6) "6381"
.........忽略............
```

## 4.sentinel sentinels

展示指定的Sentinel节点集合（不包含当前Sentinel节点），例如：

```text
127.0.0.1:26379> sentinel sentinels mymaster-1
1) 1) "name"
   2) "127.0.0.1:26380"
   3) "ip"
   4) "127.0.0.1"
   5) "port"
   6) "26380"
.........忽略............
2) 1) "name"
   2) "127.0.0.1:26381"
   3) "ip"
   4) "127.0.0.1"
   5) "port"
   6) "26381"
.........忽略............
```

## 5.sentinel get-master-addr-by-name

返回指定主节点的IP地址和端口，例如：

```text
127.0.0.1:26379> sentinel get-master-addr-by-name mymaster-1
1) "127.0.0.1"
2) "6379"
```

## 6.sentinel reset

当前Sentinel节点对符合（通配符风格）主节点的配置进行重 置，包含清除主节点的相关状态（例如故障转移），重新发现从节点和 Sentinel节点。

例如sentinel-1节点对mymaster-1节点重置状态如下：

```text
127.0.0.1:26379> sentinel reset mymaster-1
(integer) 1
```

## 7.sentinel failover

对指定主节点进行强制故障转移（没有和其他Sentinel节点“协商”），当故障转移完成后，其他Sentinel节点按照故障转移的结果更 新自身配置，这个命令在Redis Sentinel的日常运维中非常有用，将在9.6节进 行详细介绍。

例如，对mymaster-2进行故障转移：

```text
127.0.0.1:26379> sentinel failover mymaster-2
OK
```

执行命令前，mymaster-2是127.0.0.1:6382

```text
127.0.0.1:26379> info sentinel
# Sentinel
sentinel_masters:2
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
master0:name=mymaster-2,status=ok,address=127.0.0.1:6382,slaves=2,sentinels=3
master1:name=mymaster-1,status=ok,address=127.0.0.1:6379,slaves=2,sentinels=3
```

执行命令后：mymaster-2由原来的一个从节点127.0.0.1:6383代替。

```text
127.0.0.1:26379> info sentinel1
# Sentinel
sentinel_masters:2
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
master0:name=mymaster-2,status=ok,address=127.0.0.1:6383,slaves=2,sentinels=3
master1:name=mymaster-1,status=ok,address=127.0.0.1:6379,slaves=2,sentinels=3
```

## 8.sentinel ckquorum

检测当前可达的Sentinel节点总数是否达到的个数。例如 quorum=3，而当前可达的Sentinel节点个数为2个，那么将无法进行故障转 移，Redis Sentinel的高可用特性也将失去。

```text
127.0.0.1:26379> sentinel ckquorum mymaster-1
OK 3 usable Sentinels. Quorum and failover authorization can be reached
```

## 9.sentinel flushconfig

将Sentinel节点的配置强制刷到磁盘上，这个命令Sentinel节点自身用得 比较多，对于开发和运维人员只有当外部原因（例如磁盘损坏）造成配置文 件损坏或者丢失时，这个命令是很有用的。

```text
127.0.0.1:26379> sentinel flushconfig
OK
```

## 10.sentinel remove

取消当前Sentinel节点对于指定主节点的监控。 例如sentinel-1当前对mymaster-1进行了监控：

```text
127.0.0.1:26379> info sentinel
# Sentinel
sentinel_masters:2
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
master0:name=mymaster-2,status=ok,address=127.0.0.1:6382,slaves=2,sentinels=3
master1:name=mymaster-1,status=ok,address=127.0.0.1:6379,slaves=2,sentinels=3
```

例如下面，sentinel-1节点取消对mymaster-1节点的监控，但是要注意这 个命令仅仅对当前Sentinel节点有效。

```text
127.0.0.1:26379> sentinel remove mymaster-1
OK
```

再执行info sentinel命令，发现sentinel-1已经失去对mymaster-1的监控：

```text
127.0.0.1:26379> info sentinel
# Sentinel
sentinel_masters:2
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
master0:name=mymaster-2,status=ok,address=127.0.0.1:6383,slaves=2,sentinels=3
```

## 11.sentinel monitor

这个命令和配置文件中的含义是完全一样的，只不过是通过命令的形式 来完成Sentinel节点对主节点的监控。例如命令sentinel-1节点重新监控mymaster-1节点：

```text
127.0.0.1:26379> sentinel monitor mymaster-1 127.0.0.1 6379 2
OK
```

命令执行后，发现sentinel-1节点重新对mymaster-1节点进行监控：

```text
127.0.0.1:26379> info sentinel
# Sentinel
sentinel_masters:2
sentinel_tilt:0
sentinel_running_scripts:0
sentinel_scripts_queue_length:0
master0:name=mymaster-2,status=ok,address=127.0.0.1:6382,slaves=2,sentinels=3
master1:name=mymaster-1,status=ok,address=127.0.0.1:6379,slaves=2,sentinels=3
```

## 12.sentinel set

动态修改Sentinel节点配置选项，这个命令已经在[配置优化](an-zhuang-he-bu-shu/pei-zhi-you-hua.md)进行了说明，这里就不赘述了。

## 13.sentinel is-master-down-by-addr

Sentinel节点之间用来交换对主节点是否下线的判断，根据参数的不 同，还可以作为Sentinel领导者选举的通信方式，具体细节实现原理节介绍。

