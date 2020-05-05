# 设置并返回原值

将给定 key 的值设为 value ，并返回 key 的旧值\(old value\)。

当 key 存在但不是字符串类型时，返回一个错误。

```text
getset key value
```

getset和set一样会设置值，但是不同的是，它同时会返回键原来的值， 例如：

![](../../.gitbook/assets/image%20%289%29.png)

