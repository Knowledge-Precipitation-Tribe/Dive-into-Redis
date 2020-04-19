# 设置值

```text
hset key field value
```

如果设置成功会返回1，反之会返回0。此外Redis提供了hsetnx命令，它 们的关系就像set和setnx命令一样，只不过作用域由键变为field。

![](../../.gitbook/assets/image%20%28109%29.png)

