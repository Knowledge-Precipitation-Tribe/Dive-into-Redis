# 计算成员的排名

```text
zrank key member
zrevrank key member
```

zrank是从分数从低到高返回排名，zrevrank反之。例如下面操作中，tom在zrank和zrevrank分别排名第5和第0\(排名从0开始计算\)。

![](../../.gitbook/assets/image%20%2832%29.png)

