# 设置值

将字符串值 value 关联到 key 。

如果 key 已经持有其他值， SET 就覆写旧值，无视类型。

对于某个原本带有生存时间（TTL）的键来说， 当 _SET_ 命令成功在这个键上执行时， 这个键原有的 TTL 将被清除。

```text
set key value [expiration EX seconds|PX milliseconds] [NX|XX]
```

set命令有几个选项：

* ex seconds：为键设置秒级过期时间。 
* px milliseconds：为键设置毫秒级过期时间。 
* nx：键必须不存在，才可以设置成功，用于添加。 
* xx：与nx相反，键必须存在，才可以设置成功，用于更新。

除了set选项，Redis还提供了setex和setnx两个命令，它们的作用和ex和nx选项是一样的。

```text
setex key seconds value
setnx key value
```

![](../../.gitbook/assets/image%20%28126%29.png)

setnx和setxx在实际使用中有什么应用场景吗？以setnx命令为例子，由于Redis的单线程命令处理机制，如果有多个客户端同时执行setnx key value， 根据setnx的特性只有一个客户端能设置成功，setnx可以作为分布式锁的一种实现方案，Redis官方给出了使用setnx实现分布式锁的方法：[http://redis.io/topics/distlock](http://redis.io/topics/distlock。)

