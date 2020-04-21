# 交集

我们现在创建两个有序集合

![](../../.gitbook/assets/image%20%2869%29.png)

![](../../.gitbook/assets/image%20%285%29.png)

### 求交集命令

```text
zinterstore destination numkeys key [key ...] [weights weight [weight ...]] [aggregate sum|min|max]
```

这个命令参数较多，下面分别进行说明：

* destination：交集计算结果保存到这个键。
* numkeys：需要做交集计算键的个数。
* key\[key...\]：需要做交集计算的键。
* weights weight\[weight...\]：每个键的权重，在做交集计算时，每个键中的每个member会将自己分数乘以这个权重，每个键的权重默认是1。
* aggregate sum\|min\|max：计算成员交集后，分值可以按照sum（和）、 min（最小值）、max（最大值）做汇总，默认值是sum。

下面操作对user:ranking:1和user:ranking:2做交集，weights和aggregate使用了默认配置，可以看到目标键user:ranking:1\_inter\_2对分值做了sum操作：

![](../../.gitbook/assets/image%20%2884%29.png)

