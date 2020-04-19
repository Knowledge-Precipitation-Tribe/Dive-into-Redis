# 删除指定元素

```text
lrem key count value
```

lrem命令会从列表中找到等于value的元素进行删除，根据count的不同 分为三种情况：

* count&gt;0，从左到右，删除最多count个元素。
* count&lt;0，从右到左，删除最多count绝对值个元素。
* count=0，删除所有。

例如向列表从左向右插入5个a，那么当前列表变为“a a a a a java b a c b”， 下面操作将从列表左边开始删除4个为a的元素：

![](../../.gitbook/assets/image%20%2887%29.png)

