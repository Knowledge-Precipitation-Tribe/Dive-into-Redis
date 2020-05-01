# RDB

RDB持久化是把当前进程数据生成快照保存到硬盘的过程，触发RDB持久化过程分为手动触发和自动触发。

### 触发机制

手动触发分别对应save和bgsave命令：

* save命令：阻塞当前Redis服务器，直到RDB过程完成为止，对于内存比较大的实例会造成长时间阻塞，线上环境不建议使用。
* bgsave命令：Redis进程执行fork操作创建子进程，RDB持久化过程由子进程负责，完成后自动结束。阻塞只发生在fork阶段，一般时间很短。

除了执行命令手动触发之外，Redis内部还存在自动触发RDB的持久化 机制，例如以下场景：

* 使用save相关配置，如“save m n”。表示m秒内数据集存在n次修改 时，自动触发bgsave。
* 如果从节点执行全量复制操作，主节点自动执行bgsave生成RDB文件并发送给从节点。
* 执行debug reload命令重新加载Redis时，也会自动触发save操作。
* 默认情况下执行shutdown命令时，如果没有开启AOF持久化功能则自动执行bgsave。

### 执行流程

![](../.gitbook/assets/image%20%28124%29.png)

### RDB文件的处理

保存：RDB文件保存在dir配置指定的目录下，文件名通过dbfilename配 置指定。可以通过执行config set dir{newDir}和config set dbfilename{newFileName}运行期动态执行，当下次运行时RDB文件会保存到新目录。

压缩：Redis默认采用LZF算法对生成的RDB文件做压缩处理，压缩后的文件远远小于内存大小，默认开启，可以通过参数config set rdbcompression{yes\|no}动态修改。

校验：如果Redis加载损坏的RDB文件时拒绝启动，并打印如下日志：

```text
# Short read or OOM loading DB. Unrecoverable error, aborting now.
```

这时可以使用Redis提供的redis-check-dump工具检测RDB文件并获取对应的错误报告。

### RDB的优缺点

#### 优点

* RDB是一个紧凑压缩的二进制文件，代表Redis在某个时间点上的数据快照。非常适用于备份，全量复制等场景。比如每6小时执行bgsave备份， 并把RDB文件拷贝到远程机器或者文件系统中（如hdfs），用于灾难恢复。
* Redis加载RDB恢复数据远远快于AOF的方式。

### 缺点

* RDB方式数据没办法做到实时持久化/秒级持久化。因为bgsave每次运行都要执行fork操作创建子进程，属于重量级操作，频繁执行成本过高。
* RDB文件使用特定二进制格式保存，Redis版本演进过程中有多个格式的RDB版本，存在老版本Redis服务无法兼容新版RDB格式的问题。

针对RDB不适合实时持久化的问题，Redis提供了AOF持久化方式来解 决。

