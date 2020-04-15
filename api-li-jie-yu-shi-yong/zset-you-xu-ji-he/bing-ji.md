# 并集

```text
zunionstore destination numkeys key [key ...] [weights weight [weight ...]] [aggregate sum|min|max]
```

该命令的所有参数和zinterstore是一致的，只不过是做并集计算，例如 下面操作是计算user:ranking:1和user:ranking:2的并集，weights和aggregate使用了默认配置，可以看到目标键user:ranking:1\_union\_2对分值做了sum操作：

![](../../.gitbook/assets/image%20%2893%29.png)

