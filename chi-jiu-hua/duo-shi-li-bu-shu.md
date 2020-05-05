# 多实例部署

Redis单线程架构导致无法充分利用CPU多核特性，通常的做法是在一 台机器上部署多个Redis实例。当多个实例开启AOF重写后，彼此之间会产生对CPU和IO的竞争。本节主要介绍针对这种场景的分析和优化。

上一节介绍了持久化相关的子进程开销。对于单机多Redis部署，如果 同一时刻运行多个子进程，对当前系统影响将非常明显，因此需要采用一种措施，把子进程工作进行隔离。Redis在info Persistence中为我们提供了监控子进程运行状况的度量指标，如表所示。

![](../.gitbook/assets/image%20%282%29.png)

我们基于以上指标，可以通过外部程序轮询控制AOF重写操作的执行， 整个过程如图所示。

![](../.gitbook/assets/image%20%28186%29.png)

流程说明：

1. 外部程序定时轮询监控机器（machine）上所有Redis实例。
2. 对于开启AOF的实例，查看（aof\_current\_size-aof\_base\_size）/aof\_base\_size确认增长率。
3. 当增长率超过特定阈值（如100%），执行bgrewriteaof命令手动触发当前实例的AOF重写。
4. 运行期间循环检查aof\_rewrite\_in\_progress和

   aof\_current\_rewrite\_time\_sec指标，直到AOF重写结束。

5. 确认实例AOF重写完成后，再检查其他实例并重复2~4步操作。 从而保证机器内每个Redis实例AOF重写串行化执行。



