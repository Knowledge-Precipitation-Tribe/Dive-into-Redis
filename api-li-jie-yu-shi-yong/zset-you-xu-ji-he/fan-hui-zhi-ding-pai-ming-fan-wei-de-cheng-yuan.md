# 返回指定排名范围的成员

```text
zrange key start end [withscores]
zrevrange key start end [withscores]
```

有序集合是按照分值排名的，zrange是从低到高返回，zrevrange反之。 下面代码返回排名最低的是三个成员，如果加上withscores选项，同时会返回成员的分数：

![](../../.gitbook/assets/image%20%2822%29.png)

