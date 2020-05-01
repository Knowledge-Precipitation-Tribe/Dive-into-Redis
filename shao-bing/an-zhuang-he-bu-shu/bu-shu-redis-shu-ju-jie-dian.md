# 部署Redis数据节点

之前提到过，Redis Sentinel中Redis数据节点没有做任何特殊配置，按照之前章节介绍的方法启动就可以，下面以一个比较简单的配置进行说明。

## 启动主节点

配置

{% code title="redis-6379.conf" %}
```text
port 6379
daemonize yes logfile "6379.log"
dbfilename "dump-6379.rdb"
dir "/opt/soft/redis/data/"
```
{% endcode %}

启动主节点

```text
redis-server redis-6379.conf
```

确认是否启动，一般来说只需要ping命令检测一下就可以，确认Redis数据节点是否已经启动。

```text
$ redis-cli -h 127.0.0.1 -p 6379 ping
PONG
```

## 启动两个从结点

配置：

两个从节点的配置是完全一样的，下面以一个从节点为例子进行说明， 和主节点的配置不一样的是添加了slaveof配置。

{% code title="redis-6380.conf" %}
```text
port 6380
daemonize yes
logfile "6380.log"
dbfilename "dump-6380.rdb"
dir "/opt/soft/redis/data/"
slaveof 127.0.0.1 6379
```
{% endcode %}

启动两个从节点：

```text
redis-server redis-6380.conf
redis-server redis-6381.conf
```

验证：

```text
$ redis-cli -h 127.0.0.1 -p 6380 ping
PONG
$ redis-cli -h 127.0.0.1 -p 6381 ping
PONG
```

## 确认主从关系

主节点的视角，它有两个从节点，分别是127.0.0.1:6380和127.0.0.1: 6381：

```text
$ redis-cli -h 127.0.0.1 -p 6379 info replication
# Replication
role:master
connected_slaves:2
slave0:ip=127.0.0.1,port=6380,state=online,offset=281,lag=1
slave1:ip=127.0.0.1,port=6381,state=online,offset=281,lag=0
.................
```

从节点的视角，它的主节点是127.0.0.1:6379：

```text
$ redis-cli -h 127.0.0.1 -p 6380 info replication
# Replication
role:slave master_host:127.0.0.1
master_port:6379
master_link_status:up
.................
```

此时拓扑结构如图所示。

![](../../.gitbook/assets/image%20%28144%29.png)

