# Pipeline

Redis客户端执行一条命令分为如下四个过程：

1. 发送命令
2. 命令排队
3. 命令执行
4. 返回结果

其中1+4称为Round Trip Time（RTT，往返时间）

Redis提供了批量操作命令（例如mget、mset等），有效地节约RTT。但 大部分命令是不支持批量操作的，例如要执行n次hgetall命令，并没有mhgetall命令存在，需要消耗n次RTT。Redis的客户端和服务端可能部署在不同的机器上。例如客户端在北京，Redis服务端在上海，两地直线距离约为1300公里，那么1次RTT时间=1300×2/（300000×2/3）=13毫秒（光在真空中 传输速度为每秒30万公里，这里假设光纤为光速的2/3），那么客户端在1秒内大约只能执行80次左右的命令，这个和Redis的高并发高吞吐特性背道而驰。

Pipeline（流水线）机制能改善上面这类问题，它能将一组Redis命令进行组装，通过一次RTT传输给Redis，再将这组Redis命令的执行结果按顺序返回给客户端。

Pipeline并不是什么新的技术或机制，很多技术上都使用过。而且RTT在不同网络环境下会有不同，例如同机房和同机器会比较快，跨机房跨地区会比较慢。Redis命令真正执行的时间通常在微秒级别，所以才会有Redis性能瓶颈是网络这样的说法。

redis-cli的--pipe选项实际上就是使用Pipeline机制，例如下面操作将set hello world和incr counter两条命令组装：

```text
echo -en '*3\r\n$3\r\nSET\r\n$5\r\nhello\r\n$5\r\nworld\r\n*2\r\n$4\r\nincr\r\ n$7\r\ncounter\r\n' | redis-cli --pipe
```

但大部分开发人员更倾向于使用高级语言客户端中的Pipeline，目前大部分Redis客户端都支持Pipeline，例如通过Java的Redis客户端Jedis使用Pipeline功能。

