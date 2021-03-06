# AOF

## 什么是AOF

AOF（append only file）持久化：**以独立日志的方式记录每次写命令**，重启时再重新执行AOF文件中的命令达到恢复数据的目的。AOF的主要作用是解决了数据持久化的实时性，目前已经是Redis持久化的主流方式。理解掌握好AOF持久化机制对我们兼顾数据安全性和性能非常有帮助。

## 使用AOF

开启AOF功能需要设置配置：appendonly yes，默认不开启。AOF文件名通过appendfilename配置设置，默认文件名是appendonly.aof。保存路径同 RDB持久化方式一致，通过dir配置指定。AOF的工作流程操作：命令写入 （append）、文件同步（sync）、文件重写（rewrite）、重启加载 （load），如图所示。

![](../.gitbook/assets/image%20%28110%29.png)

流程如下：

* 所有的写入命令会追加到aof\_buf（缓冲区）中。
* AOF缓冲区根据对应的策略向硬盘做同步操作。
* 随着AOF文件越来越大，需要定期对AOF文件进行重写，达到压缩的目的。
* 当Redis服务器重启时，可以加载AOF文件进行数据恢复。

## 命令写入

AOF命令写入的内容直接是文本协议格式。例如set hello world这条命令，在AOF缓冲区会追加如下文本：

```text
*3\r\n$3\r\nset\r\n$5\r\nhello\r\n$5\r\nworld\r\n
```

下面介绍关于AOF的两个疑惑：

* AOF为什么直接采用文本协议格式？可能的理由如下：
  * 文本协议具有很好的兼容性。
  * 开启AOF后，所有写入命令都包含追加操作，直接采用协议格式，避免了二次处理开销。
  * ·文本协议具有可读性，方便直接修改和处理。
* AOF为什么把命令追加到aof\_buf中？Redis使用单线程响应命令，如 果每次写AOF文件命令都直接追加到硬盘，那么性能完全取决于当前硬盘负载。先写入缓冲区aof\_buf中，还有另一个好处，Redis可以提供多种缓冲区同步硬盘的策略，在性能和安全性方面做出平衡。

## 文件同步

Redis提供了多种AOF缓冲区同步文件策略，由参数appendfsync控制， 不同值的含义如表所示。

![](../.gitbook/assets/image%20%2822%29.png)

系统调用write和fsync说明：

* write操作会触发延迟写（delayed write）机制。Linux在内核提供页缓冲区用来提高硬盘IO性能。write操作在写入系统缓冲区后直接返回。同步硬盘操作依赖于系统调度机制，例如：缓冲区页空间写满或达到特定时间周期。同步文件之前，如果此时系统故障宕机，缓冲区内数据将丢失。
* fsync针对单个文件操作（比如AOF文件），做强制硬盘同步，fsync将阻塞直到写入硬盘完成后返回，保证了数据持久化。
* 配置为always时，每次写入都要同步AOF文件，在一般的SATA硬盘上，Redis只能支持大约几百TPS写入，显然跟Redis高性能特性背道而驰， 不建议配置。
* 配置为no，由于操作系统每次同步AOF文件的周期不可控，而且会加大每次同步硬盘的数据量，虽然提升了性能，但数据安全性无法保证。
* 配置为everysec，是建议的同步策略，也是默认配置，做到兼顾性能和数据安全性。理论上只有在系统突然宕机的情况下丢失1秒的数据。

## 重写机制

随着命令不断写入AOF，文件会越来越大，为了解决这个问题，Redis 引入AOF重写机制压缩文件体积。AOF文件重写是把Redis进程内的数据转 化为写命令同步到新AOF文件的过程。

重写后的AOF文件为什么可以变小？有如下原因：

* 进程内已经超时的数据不再写入文件。
* 旧的AOF文件含有无效命令，如del key1、hdel key2、srem keys、set a111、set a222等。重写使用进程内数据直接生成，这样新的AOF文件只保留最终数据的写入命令。
* 多条写命令可以合并为一个，如：lpush list a、lpush list b、lpush list c可以转化为：lpush list a b c。为了防止单条命令过大造成客户端缓冲区溢出，对于list、set、hash、zset等类型操作，以64个元素为界拆分为多条。

AOF重写降低了文件占用空间，除此之外，另一个目的是：更小的AOF文件可以更快地被Redis加载。

AOF重写过程可以手动触发和自动触发：

* 手动触发：直接调用bgrewriteaof命令。
* 自动触发：根据auto-aof-rewrite-min-size和auto-aof-rewrite-percentage参数确定自动触发时机。
  * auto-aof-rewrite-min-size：表示运行AOF重写时文件最小体积，默认为64MB。
  * auto-aof-rewrite-percentage：代表当前AOF文件空间 （aof\_current\_size）和上一次重写后AOF文件空间（aof\_base\_size）的比值。

自动触发时机=aof\_current\_size&gt;auto-aof-rewrite-minsize&&（aof\_current\_size-aof\_base\_size）/aof\_base\_size&gt;=auto-aof-rewritepercentage

其中aof\_current\_size和aof\_base\_size可以在info Persistence统计信息中查看。

## 执行流程

![](../.gitbook/assets/image%20%28111%29.png)

## 文件校验

加载损坏的AOF文件时会拒绝启动，并打印如下日志：

```text
# Bad file format reading the append only file: make a backup of your AOF file, then use ./redis-check-aof --fix <filename>
```

对于错误格式的AOF文件，先进行备份，然后采用redis-check-aof--fix命令进行修复，修复后使用diff-u对比数据的差异，找出丢失的数据，有些可以人工修改补全。

AOF文件可能存在结尾不完整的情况，比如机器突然掉电导致AOF尾部文件命令写入不全。Redis为我们提供了aof-load-truncated配置来兼容这种情况，默认开启。加载AOF时，当遇到此问题时会忽略并继续启动，同时打印如下警告日志：

```text
# !!! Warning: short read while loading the AOF file !!!
# !!! Truncating the AOF at offset 397856725 !!!
# AOF loaded anyway because aof-load-truncated is enabled
```

## AOF优缺点

### 优点

* 数据安全，aof 持久化可以配置appendfsync 属性可以配置为always，每进行一次命令操作就记录到 aof 文件中一次。
* 通过 append 模式写文件，即使中途服务器宕机，可以通过 redis-check-aof 工具解决数据一致性问题。
* AOF 机制的 rewrite 模式。AOF 文件没被 rewrite 之前（文件过大时会对命令进行合并重写），可以删除其中的某些命令（比如误操作的flushall）\)

### 缺点

* AOF 文件比 RDB 文件大，且恢复速度慢。
* 数据集大的时候，比RDB启动效率低。



