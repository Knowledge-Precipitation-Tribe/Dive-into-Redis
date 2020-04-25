# 配置优化

了解每个配置的含义有助于更加合理地使用Redis Sentinel，因此本节将对每个配置的使用和优化进行详细介绍。Redis安装目录下有一个sentinel.conf，是默认的Sentinel节点配置文件，下面就以它作为例子进行说 明。

## 配置说明和优化

```text
port 26379
dir /opt/soft/redis/data
sentinel monitor mymaster 127.0.0.1 6379 2
sentinel down-after-milliseconds mymaster 30000
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 180000
#sentinel auth-pass <master-name> <password>
#sentinel notification-script <master-name> <script-path>
#sentinel client-reconfig-script <master-name> <script-path>
```

port和dir分别代表Sentinel节点的端口和工作目录，下面重点对sentinel相关配置进行详细说明。

（1）sentinel monitor

配置如下：

```text
sentinel monitor <master-name> <ip> <port> <quorum>
```

Sentinel节点会定期监控主节点，所以从配置上必然也会有所体现，本配置说明Sentinel节点要监控的是一个名字叫做，ip地址和端口的主节点。代表要判定主节点最终不可达所需要的票数。但实际上Sentinel节点会对所有节点进行监控，但是在Sentinel节点的配置中没有看到有关从节点和其余Sentinel节点的配置，那是因为Sentinel节点会从主节点中获取有关从节点以及其余Sentinel节点的相关信息。

例如某个Sentinel初始节点配置如下：

```text
port 26379
daemonize yes
logfile "26379.log"
dir /opt/soft/redis/data
sentinel monitor mymaster 127.0.0.1 6379 2
sentinel down-after-milliseconds mymaster 30000
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 180000
```

当所有节点启动后，配置文件中的内容发生了变化，体现在三个方面： 

* Sentinel节点自动发现了从节点、其余Sentinel节点。 
* 去掉了默认配置，例如parallel-syncs、failover-timeout参数。 
* 添加了配置纪元相关参数。 启动后变化为：

![](../../.gitbook/assets/image%20%2851%29.png)

参数用于故障发现和判定，例如将quorum配置为2，代表至少有2个Sentinel节点认为主节点不可达，那么这个不可达的判定才是客观的。 对于设置的越小，那么达到下线的条件越宽松，反之越严格。一般建议将其设置为Sentinel节点的一半加1。

同时还与Sentinel节点的领导者选举有关，至少要有 max（quorum，num（sentinels）/2+1）个Sentinel节点参与选举，才能选出领导者Sentinel，从而完成故障转移。例如有5个Sentinel节点，quorum=4，那么 至少要有max（quorum，num（sentinels）/2+1）=4个在线Sentinel节点才可以进行领导者选举。

（2）sentinel down-after-milliseconds

配置如下：

```text
sentinel down-after-milliseconds <master-name> <times>
```

每个Sentinel节点都要通过定期发送ping命令来判断Redis数据节点和其余Sentinel节点是否可达，如果超过了down-after-milliseconds配置的时间且没有有效的回复，则判定节点不可达，（单位为毫秒）就是超时时 间。这个配置是对节点失败判定的重要依据。

**优化说明：**down-after-milliseconds越大，代表Sentinel节点对于节点不可达的条件越宽松，反之越严格。条件宽松有可能带来的问题是节点确实不可 达了，那么应用方需要等待故障转移的时间越长，也就意味着应用方故障时 间可能越长。条件严格虽然可以及时发现故障完成故障转移，但是也存在一定的误判率。

down-after-milliseconds虽然以为参数，但实际上对Sentinel节点、主节点、从节点的失败判定同时有效。

（3）sentinel parallel-syncs

配置如下：

```text
sentinel parallel-syncs <master-name> <nums>
```

当Sentinel节点集合对主节点故障判定达成一致时，Sentinel领导者节点 会做故障转移操作，选出新的主节点，原来的从节点会向新的主节点发起复 制操作，parallel-syncs就是用来限制在一次故障转移之后，每次向新的主节 点发起复制操作的从节点个数。如果这个参数配置的比较大，那么多个从节 点会向新的主节点同时发起复制操作，尽管复制操作通常不会阻塞主节点， 但是同时向主节点发起复制，必然会对主节点所在的机器造成一定的网络和 磁盘IO开销。图9-17展示parallel-syncs=3和parallel-syncs=1的效果，parallelsyncs=3会同时发起复制，parallel-syncs=1时从节点会轮询发起复制。

（4）sentinel failover-timeout

配置如下：

```text
sentinel failover-timeout <master-name> <times>
```

![](../../.gitbook/assets/image%20%28135%29.png)

failover-timeout通常被解释成故障转移超时时间，但实际上它作用于故 障转移的各个阶段：

a）选出合适从节点。

 b）晋升选出的从节点为主节点。 

c）命令其余从节点复制新的主节点。 

d）等待原主节点恢复后命令它去复制新的主节点。 

failover-timeout的作用具体体现在四个方面： 

1）如果Redis Sentinel对一个主节点故障转移失败，那么下次再对该主 节点做故障转移的起始时间是failover-timeout的2倍。 

2）在b）阶段时，如果Sentinel节点向a）阶段选出来的从节点执行slaveof no one一直失败（例如该从节点此时出现故障），当此过程超过failover-timeout时，则故障转移失败。

3）在b）阶段如果执行成功，Sentinel节点还会执行info命令来确认a） 阶段选出来的节点确实晋升为主节点，如果此过程执行时间超过failovertimeout时，则故障转移失败。

4）如果c）阶段执行时间超过了failover-timeout（不包含复制时间）， 则故障转移失败。注意即使超过了这个时间，Sentinel节点也会最终配置从 节点去同步最新的主节点。

（5）sentinel auth-pass

配置如下：

```text
sentinel auth-pass <master-name> <password>
```

如果Sentinel监控的主节点配置了密码，sentinel auth-pass配置通过添加 主节点的密码，防止Sentinel节点对主节点无法监控。

（6）sentinel notification-script

配置如下：

```text
sentinel notification-script <master-name> <script-path>
```

sentinel notification-script的作用是在故障转移期间，当一些警告级别的 Sentinel事件发生（指重要事件，例如-sdown：客观下线、-odown：主观下 线）时，会触发对应路径的脚本，并向脚本发送相应的事件参数。

例如在/opt/redis/scripts/下配置了notification.sh，该脚本会接收每个 Sentinel节点传过来的事件参数，可以利用这些参数作为邮件或者短信报警依据：

```text
#!/bin/sh
#获取所有参数
msg=$*
#报警脚本或者接口，将msg作为参数
exit 0
```

如果需要该功能，就可以在Sentinel节点添加如下配置（=mymaster）：

```text
sentinel notification-script mymaster /opt/redis/scripts/notification.sh
```

例如下面就是某个Sentinel节点对主节点做了主观下线（有关主观下线 的概念将在9.5节进行详细介绍）后脚本收到的参数：

（7）sentinel client-reconfig-script

配置如下：

```text
sentinel client-reconfig-script <master-name> <script-path>
```

sentinel client-reconfig-script的作用是在故障转移结束后，会触发对应路 径的脚本，并向脚本发送故障转移结果的相关参数。和notification-script类 似，可以在/opt/redis/scripts/下配置了client-reconfig.sh，该脚本会接收每个 Sentinel节点传过来的故障转移结果参数，并触发类似短信和邮件报警：

```text
#!/bin/sh
#获取所有参数
msg=$*
#报警脚本或者接口，将msg作为参数
exit 0
```

如果需要该功能，就可以在Sentinel节点添加如下配置（=mymaster）：

```text
sentinel client-reconfig-script mymaster /opt/redis/scripts/client-reconfig.sh
```

当故障转移结束，每个Sentinel节点会将故障转移的结果发送给对应的 脚本，具体参数如下：

```text
<master-name> <role> <state> <from-ip> <from-port> <to-ip> <to-port>
```

* &lt;master-name&gt;：主节点名。 
* &lt;role&gt;：Sentinel节点的角色，分别是leader和observer，leader代表当前 Sentinel节点是领导者，是它进行的故障转移；observer是其余Sentinel节点。 
* &lt;from-ip&gt;：原主节点的ip地址。 
* &lt;from-port&gt;：原主节点的端口。 
* &lt;to-ip&gt;：新主节点的ip地址。 
* &lt;to-port&gt;：新主节点的端口。 例如以下内容分别是三个Sentinel节点发送给脚本的，其中一个是 leader，另外两个是observer：

例如以下内容分别是三个Sentinel节点发送给脚本的，其中一个是 leader，另外两个是observer：

```text
mymaster leader start 127.0.0.1 6379 127.0.0.1 6380
mymaster observer start 127.0.0.1 6379 127.0.0.1 6380
mymaster observer start 127.0.0.1 6379 127.0.0.1 6380
```

有关sentinel notification-script和sentinel client-reconfig-script有几点需要 注意：

* &lt;script-path&gt;必须有可执行权限。 
* &lt;script-path&gt;开头必须包含shell脚本头（例如\#！/bin/sh），否则事件发 生时Redis将无法执行脚本产生如下错误：

```text
-script-error /opt/sentinel/notification.sh 0 2
```

* Redis规定脚本的最大执行时间不能超过60秒，超过后脚本将被杀掉。 
* 如果shell脚本以exit 1结束，那么脚本稍后重试执行。如果以exit 2或者 更高的值结束，那么脚本不会重试。正常返回值是exit 0。 
* 如果需要运维的Redis Sentinel比较多，建议不要使用这种脚本的形式 来进行通知，这样会增加部署的成本。

## 如何监控多个主节点

Redis Sentinel可以同时监控多个主节点，具体拓扑图类似于下图。 

![](../../.gitbook/assets/image%20%2820%29.png)

配置方法也比较简单，只需要指定多个masterName来区分不同的主节点 即可，例如下面的配置监控monitor master-business-1（10.10.xx.1：6379）和 monitor master-business-2（10.10.xx.2：6379）两个主节点：

![](../../.gitbook/assets/image%20%2874%29.png)

## 调整配置

和普通的Redis数据节点一样，Sentinel节点也支持动态地设置参数，而且和普通的Redis数据节点一样并不是支持所有的参数，具体使用方法如下：

```text
sentinel set <param> <value>
```

下表是sentinel set命令支持的参数。

![](../../.gitbook/assets/image%20%28170%29.png)

有几点需要注意一下： 

1）sentinel set命令只对当前Sentinel节点有效。 

2）sentinel set命令如果执行成功会立即刷新配置文件，这点和Redis普 通数据节点设置配置需要执行config rewrite刷新到配置文件不同。 

3）建议所有Sentinel节点的配置尽可能一致，这样在故障发现和转移时 比较容易达成一致。 

4）表9-3中为sentinel set支持的参数，具体可以参考源码中的sentinel.c的 sentinelSetCommand函数。 

5）Sentinel对外不支持config命令。

