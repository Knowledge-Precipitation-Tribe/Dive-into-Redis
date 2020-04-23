# 内在原因

## API或数据结构使用不合理

通常Redis执行命令速度非常快，但也存在例外，如对一个包含上万个元素的hash结构执行hgetall操作，由于数据量比较大且命令算法复杂度是 O\(n\)，这条命令执行速度必然很慢。这个问题就是典型的不合理使用API和数据结构。对于高并发的场景我们应该尽量避免在大对象上执行算法复杂度超过O\(n\)的命令。

### 如何发现慢查询

Redis原生提供慢查询统计功能，执行slowlog get{n}命令可以获取最近的n条慢查询命令，默认对于执行超过10毫秒的命令都会记录到一个定长队列中，线上实例建议设置为1毫秒便于及时发现毫秒级以上的命令。如果命令执行时间在毫秒级，则实例实际OPS只有1000左右。慢查询队列长度默认128，可适当调大。慢查询更多细节见[慢查询分析](../jin-jie-gong-neng/man-cha-xun-fen-xi/)。慢查询本身只记录了命令执行 时间，不包括数据网络传输时间和命令排队时间，因此客户端发生阻塞异常后，可能不是当前命令缓慢，而是在等待其他命令执行。需要重点比对异常和慢查询发生的时间点，确认是否有慢查询造成的命令阻塞排队。

发现慢查询后，开发人员需要作出及时调整。可以按照以下两个方向去调整：

1）修改为低算法度的命令，如hgetall改为hmget等，禁用keys、sort等命令。

2）调整大对象：缩减大对象数据或把大对象拆分为多个小对象，防止一次命令操作过多的数据。大对象拆分过程需要视具体的业务决定，如用户好友集合存储在Redis中，有些热点用户会关注大量好友，这时可以按时间或其他维度拆分到多个集合中。

### 如何发现大对象

Redis本身提供发现大对象的工具，对应命令：redis-cli-h{ip}p{port}bigkeys。内部原理采用分段进行scan操作，把历史扫描过的最大对象 统计出来便于分析优化，运行效果如下：

![](../.gitbook/assets/image%20%2813%29.png)

根据结果汇总信息能非常方便地获取到大对象的键，以及不同类型数据结构的使用情况。

## CPU饱和

单线程的Redis处理命令时只能使用一个CPU。而CPU饱和是指Redis把单核CPU使用率跑到接近100%。使用top命令很容易识别出对应Redis进程的CPU使用率。CPU饱和是非常危险的，将导致Redis无法处理更多的命令，严重影响吞吐量和应用方的稳定性。对于这种情况，首先判断当前Redis的并发量是否达到极限，建议使用统计命令redis-cli-h{ip}-p{port}--stat获取当前Redis使用情况，该命令每秒输出一行统计信息，运行效果如下：

![](../.gitbook/assets/image%20%2817%29.png)

以上输出是一个接近饱和的Redis实例的统计信息，它每秒平均处理6万+的请求。对于这种情况，垂直层面的命令优化很难达到效果，这时就需要做集群化水平扩展来分摊OPS压力。如果只有几百或几千OPS的Redis实例就接近CPU饱和是很不正常的，有可能使用了高算法复杂度的命令。还有一种情况是过度的内存优化，这种情况有些隐蔽，需要我们根据info commandstats统计信息分析出命令不合理开销时间，例如下面的耗时统计：

```text
cmdstat_hset:calls=198757512,usec=27021957243,usec_per_call=135.95
```

查看这个统计可以发现一个问题，hset命令算法复杂度只有O\(1\)但平均耗时却达到135微秒，显然不合理，正常情况耗时应该在10微秒以下。这是因为上面的Redis实例为了追求低内存使用量，过度放宽ziplist使用条件 （修改了hash-max-ziplist-entries和hash-max-ziplist-value配置）。进程内的hash对象平均存储着上万个元素，而针对ziplist的操作算法复杂度在O\(n\) 到O\(n2\)之间。虽然采用ziplist编码后hash结构内存占用会变小，但是操作变得更慢且更消耗CPU。ziplist压缩编码是Redis用来平衡空间和效率的优化手段，不可过度使用。

## 持久化阻塞

对于开启了持久化功能的Redis节点，需要排查是否是持久化导致的阻塞。持久化引起主线程阻塞的操作主要有：fork阻塞、AOF刷盘阻塞、 HugePage写操作阻塞。

### fork阻塞

fork操作发生在RDB和AOF重写时，Redis主线程调用fork操作产生共享内存的子进程，由子进程完成持久化文件重写工作。如果fork操作本身耗时过长，必然会导致主线程的阻塞。

可以执行info stats命令获取到latest\_fork\_usec指标，表示Redis最近一次 fork操作耗时，如果耗时很大，比如超过1秒，则需要做出优化调整，如避免使用过大的内存实例和规避fork缓慢的操作系统等，更多细节见[fork优化部分](../chi-jiu-hua/wen-ti-ding-wei-yu-you-hua.md#fork-cao-zuo)。

### AOF刷盘阻塞

当我们开启AOF持久化功能时，文件刷盘的方式一般采用每秒一次，后台线程每秒对AOF文件做fsync操作。当硬盘压力过大时，fsync操作需要等待，直到写入完成。如果主线程发现距离上一次的fsync成功超过2秒，为了数据安全性它会阻塞直到后台线程执行fsync操作完成。这种阻塞行为主要是硬盘压力引起，可以查看Redis日志识别出这种情况，当发生这种阻塞行为时，会打印如下日志：

```text
Asynchronous AOF fsync is taking too long (disk is busy). Writing the AOF buffer without waiting for fsync to complete, this may slow down Redis.
```

也可以查看info persistence统计中的aof\_delayed\_fsync指标，每次发生fdatasync阻塞主线程时会累加。定位阻塞问题后具体优化方法见[AOF追加阻塞部分](../chi-jiu-hua/wen-ti-ding-wei-yu-you-hua.md#aof-zhui-jia-zu-sai)。

{% hint style="info" %}
硬盘压力可能是Redis进程引起的，也可能是其他进程引起的，可以使 用iotop查看具体是哪个进程消耗过多的硬盘资源。
{% endhint %}

### HugePage写操作阻塞

子进程在执行重写期间利用Linux写时复制技术降低内存开销，因此只有写操作时Redis才复制要修改的内存页。对于开启Transparent HugePages的操作系统，每次写命令引起的复制内存页单位由4K变为2MB，放大了512 倍，会拖慢写操作的执行时间，导致大量写操作慢查询。例如简单的incr命令也会出现在慢查询中。

Redis官方文档中针对绝大多数的阻塞问题进行了分类说明，这里不再详细介绍，细节请见：[http://www.redis.io/topics/latency。](http://www.redis.io/topics/latency。)

