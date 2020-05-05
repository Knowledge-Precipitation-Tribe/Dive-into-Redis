# 批量获取值

返回所有\(一个或多个\)给定 key 的值。

如果给定的 key 里面，有某个 key 不存在，那么这个 key 返回特殊值 nil 。因此，该命令永不失败。

```text
mget key [key ...]
```

下面操作批量获取了键a、b、c的值：

![](../../.gitbook/assets/image%20%2825%29.png)

