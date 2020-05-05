# 获取值

返回 key 所关联的字符串值。

如果 key 不存在那么返回特殊值 nil 。

假如 key 储存的值不是字符串类型，返回一个错误，因为 GET 只能用于处理字符串值。

```text
get key
```

获取键hello的值

![](../../.gitbook/assets/image%20%2896%29.png)

如果要获取的键不存在，则返回nil

![](../../.gitbook/assets/image%20%28100%29.png)

