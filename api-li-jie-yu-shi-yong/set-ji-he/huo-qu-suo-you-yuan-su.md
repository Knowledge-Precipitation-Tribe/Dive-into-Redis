# 获取所有元素

```text
smembers key
```

下面代码获取集合myset所有元素，并且返回结果是无序的：

![](../../.gitbook/assets/image%20%28195%29.png)

smembers和lrange、hgetall都属于比较重的命令，如果元素过多存在阻 塞Redis的可能性，这时候可以使用sscan来完成。

