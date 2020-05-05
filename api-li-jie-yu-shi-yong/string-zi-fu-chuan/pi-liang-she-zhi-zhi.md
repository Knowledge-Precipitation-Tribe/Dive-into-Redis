# 批量设置值

同时设置一个或多个 key-value 对。

如果某个给定 key 已经存在，那么 MSET 会用新值覆盖原来的旧值，如果这不是你所希望的效果，请考虑使用 _MSETNX_ 命令：它只会在所有给定 key 都不存在的情况下进行设置操作。

MSET 是一个原子性\(atomic\)操作，所有给定 key 都会在同一时间内被设置，某些给定 key 被更新而另一些给定 key 没有改变的情况，不可能发生。

```text
mset key value [key value ...]
```

下面操作通过mset命令一次性设置3个键值对：

![](../../.gitbook/assets/image%20%2889%29.png)

