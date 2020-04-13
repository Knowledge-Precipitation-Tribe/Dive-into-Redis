# 获取所有的field-value

```text
hgetall user:1
```

获取user：1所有的field-value：

![](../../.gitbook/assets/image%20%2826%29.png)

{% hint style="info" %}
在使用hgetall时，如果哈希元素个数比较多，会存在阻塞Redis的可能。
{% endhint %}



