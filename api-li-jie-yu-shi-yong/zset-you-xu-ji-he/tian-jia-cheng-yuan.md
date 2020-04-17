# 添加成员

```text
zadd key score member [score member ...]
```

下面操作向有序集合user:ranking添加用户tom和他的分数251：

![](../../.gitbook/assets/image%20%2868%29.png)

返回结果代表成功添加成员的个数，Redis为zadd命令添加了nx、xx、ch、incr四个选项：

* nx：member必须不存在，才可以设置成功，用于添加。
* xx：member必须存在，才可以设置成功，用于更新。
* ch：返回此次操作后，有序集合元素和分数发生变化的个数
* incr：对score做增加，相当于后面介绍的zincrby。

{% hint style="info" %}
有序集合相比集合提供了排序字段，但是也产生了代价，zadd的时间 复杂度为O\(log\(n\)\)，sadd的时间复杂度为O\(1\)。
{% endhint %}

