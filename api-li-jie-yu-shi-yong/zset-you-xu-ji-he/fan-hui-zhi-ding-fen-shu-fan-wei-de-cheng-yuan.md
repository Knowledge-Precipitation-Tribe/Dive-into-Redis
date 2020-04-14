# 返回指定分数范围的成员

```text
zrangebyscore key min max [withscores] [limit offset count]
zrevrangebyscore key max min [withscores] [limit offset count]
```

其中zrangebyscore按照分数从低到高返回，zrevrangebyscore反之。例如下面操作从低到高返回200到221分的成员，withscores选项会同时返回每个成员的分数。\[limit offset count\]选项可以限制输出的起始位置和个数：

![](../../.gitbook/assets/image%20%2818%29.png)

同时min和max还支持开区间（小括号）和闭区间（中括号），-inf和 +inf分别代表无限小和无限大。

