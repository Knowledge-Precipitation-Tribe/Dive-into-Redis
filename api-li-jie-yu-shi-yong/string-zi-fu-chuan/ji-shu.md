# 计数

```text
incr key
```

incr命令用于对值做自增操作，返回结果分为三种情况：

* 值不是整数，返回错误。
* 值是整数，返回自增后的结果。
* 键不存在，按照值为0自增，返回结果为1。

![](../../.gitbook/assets/image%20%28103%29.png)

除了incr命令，Redis提供了decr（自减）、incrby（自增指定数字）、 decrby（自减指定数字）、incrbyfloat（自增浮点数）。

