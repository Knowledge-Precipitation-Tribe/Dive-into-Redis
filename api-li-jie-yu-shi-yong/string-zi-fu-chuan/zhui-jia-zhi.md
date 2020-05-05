# 追加值

如果 key 已经存在并且是一个字符串， APPEND 命令将 value 追加到 key 原来的值的末尾。

如果 key 不存在， APPEND 就简单地将给定 key 设为 value ，就像执行 SET key value 一样。

```text
append key value
```

append可以向字符串尾部追加值

![](../../.gitbook/assets/image%20%2852%29.png)

