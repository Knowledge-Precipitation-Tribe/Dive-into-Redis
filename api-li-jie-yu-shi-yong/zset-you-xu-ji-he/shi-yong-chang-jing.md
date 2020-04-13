# 使用场景

有序集合比较典型的使用场景就是排行榜系统。例如视频网站需要对用 户上传的视频做排行榜，榜单的维度可能是多个方面的：按照时间、按照播放数量、按照获得的赞数。本节使用赞数这个维度，记录每天用户上传视频的排行榜。主要需要实现以下4个功能。

### 添加用户赞数

例如用户mike上传了一个视频，并获得了3个赞，可以使用有序集合的 zadd和zincrby功能：

```text
zadd user:ranking:2020_01_01 mike 3
```

如果之后再获得一个赞，可以使用zincrby：

```text
zincrby user:ranking:2020_01_01 mike 1
```

### 取消用户赞数

由于各种原因（例如用户注销、用户作弊）需要将用户删除，此时需要将用户从榜单中删除掉，可以使用zrem。例如删除成员mike：

```text
zrem user:ranking:2020_01_01 mike
```

### 展示获取赞数最多的十个用户

此功能使用zrevrange命令实现

```text
zrevrangebyrank user:ranking:2020_01_01 0 9
```

### 展示用户信息以及用户分数

此功能将用户名作为键后缀，将用户信息保存在哈希类型中，至于用户的分数和排名可以使用zscore和zrank两个功能：

```text
hgetall user:info:tom
zscore user:ranking:2020_01_01 mike
zrank user:ranking:2020_01_01 mike
```

