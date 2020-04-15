# 计算元素个数

```text
scard key
```

scard的时间复杂度为O\(1\)，它不会遍历集合所有元素，而是直接用 Redis内部的变量，例如：

![](../../.gitbook/assets/image%20%2814%29.png)

