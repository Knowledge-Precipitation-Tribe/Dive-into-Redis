# 事务

熟悉关系型数据库的读者应该对事务比较了解，简单地说，事务表示一 组动作，要么全部执行，要么全部不执行。例如在社交网站上用户A关注了用户B，那么需要在用户A的关注表中加入用户B，并且在用户B的粉丝表中添加用户A，这两个行为要么全部执行，要么全部不执行，否则会出现数据不一致的情况。

Redis提供了简单的事务功能，将一组需要一起执行的命令放到multi和 exec两个命令之间。multi命令代表事务开始，exec命令代表事务结束，它们之间的命令是原子顺序执行的，例如下面操作实现了上述用户关注问题。

```text
127.0.0.1:6379> multi
OK
127.0.0.1:6379> sadd user:a:follow user:b
QUEUED
127.0.0.1:6379> sadd user:b:fans user:a
QUEUED
```

可以看到sadd命令此时的返回结果是QUEUED，代表命令并没有真正执行，而是暂时保存在Redis中。如果此时另一个客户端执行sismember user:a:follow user:b返回结果应该为0。

只有当exec执行后，用户A关注用户B的行为才算完成，如下所示返回的两个结果对应sadd命令。

```text
127.0.0.1:6379> exec
1) (integer) 1
2) (integer) 1
127.0.0.1:6379> sismember user:a:follow user:b
(integer) 1
```

如果要停止事务的执行，可以使用discard命令代替exec命令即可。

```text
127.0.0.1:6379> discard
OK
127.0.0.1:6379> sismember user:a:follow user:b
(integer) 0
```

如果事务中的命令出现错误，Redis的处理机制也不尽相同。

### 1.命令错误

例如下面操作错将set写成了sett，属于语法错误，会造成整个事务无法 执行，key和counter的值未发生变化：

```text
127.0.0.1:6388> mget key counter
1) "hello"
2) "100"
127.0.0.1:6388> multi
OK
127.0.0.1:6388> sett key world
(error) ERR unknown command 'sett'
127.0.0.1:6388> incr counter
QUEUED
127.0.0.1:6388> exec
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6388> mget key counter
1) "hello"
2) "100"
```

### 2.运行时错误

例如用户B在添加粉丝列表时，误把sadd命令写成了zadd命令，这种就 是运行时命令，因为语法是正确的：

```text
127.0.0.1:6379> multi
OK
127.0.0.1:6379> sadd user:a:follow user:b
QUEUED
127.0.0.1:6379> zadd user:b:fans 1 user:a
QUEUED
127.0.0.1:6379> exec
1) (integer) 1
2) (error) WRONGTYPE Operation against a key holding the wrong kind of value
127.0.0.1:6379> sismember user:a:follow user:b
(integer) 1
```

可以看到Redis并不支持回滚功能，sadd user:a:follow user:b命令已经执行成功，开发人员需要自己修复这类问题。

有些应用场景需要在事务之前，确保事务中的key没有被其他客户端修改过，才执行事务，否则不执行（类似乐观锁）。Redis提供了watch命令来解决这类问题，下表展示了两个客户端执行命令的时序。

![](../../.gitbook/assets/image%20%28118%29.png)

可以看到“客户端-1”在执行multi之前执行了watch命令，“客户端-2”在“客户端-1”执行exec之前修改了key值，造成事务没有执行（exec结果 为nil），整个代码如下所示：

```text
#T1：客户端1
127.0.0.1:6379> set key "java"
OK
#T2：客户端1
127.0.0.1:6379> watch key
OK
#T3：客户端1
127.0.0.1:6379> multi
OK
#T4：客户端2
127.0.0.1:6379> append key python
(integer) 11
#T5：客户端1
127.0.0.1:6379> append key jedis
QUEUED
#T6：客户端1
127.0.0.1:6379> exec
(nil)
#T7：客户端1
127.0.0.1:6379> get key
"javapython"
```

Redis提供了简单的事务，之所以说它简单，主要是因为它不支持事务中的回滚特性，同时无法实现命令之间的逻辑关系计算，当然也体现了Redis的“keep it simple”的特性，下一小节介绍的Lua脚本同样可以实现事务的相关功能，但是功能要强大很多。

