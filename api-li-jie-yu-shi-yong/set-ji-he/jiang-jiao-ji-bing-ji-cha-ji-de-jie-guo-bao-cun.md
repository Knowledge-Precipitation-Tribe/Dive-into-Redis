# 将交集、并集、差集的结果保存

```text
sinterstore destination key [key ...]
suionstore destination key [key ...]
sdiffstore destination key [key ...]
```

集合间的运算在元素较多的情况下会比较耗时，所以Redis提供了上面 三个命令（原命令+store）将集合间交集、并集、差集的结果保存在 destination key中，例如下面操作将user:1:follow和user:2:follow两个集合的交集结果保存在user:1\_2:inter中，user:1\_2:inter本身也是集合类型：

![](../../.gitbook/assets/image%20%2882%29.png)

