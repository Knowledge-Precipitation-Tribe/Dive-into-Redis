# 命令

### 发布消息

```text
publish channel message
```

下面操作会向channel:sports频道发布一条消息“Tim won the championship”，返回结果为订阅者个数，因为此时没有订阅，所以返回结果为0：

```text
127.0.0.1:6379> publish channel:sports "Tim won the championship"
(integer) 0
```

### 订阅消息

```text
subscribe channel [channel ...]
```

订阅者可以订阅一个或多个频道，下面操作为当前客户端订阅了channel:sports频道：

```text
127.0.0.1:6379> subscribe channel:sports
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "channel:sports"
3) (integer) 1
```

此时另一个客户端发布一条消息：

```text
127.0.0.1:6379> publish channel:sports "james get the mvp"
(integer) 1
```

当前订阅者客户端会收到如下消息：

```text
127.0.0.1:6379> subscribe channel:sports
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "channel:sports"
3) (integer) 1
1) "message"
2) "channel:sports"
3) "james get the mvp"
```

有关订阅命令有两点需要注意：

* 客户端在执行订阅命令之后进入了订阅状态，只能接收subscribe、 psubscribe、unsubscribe、punsubscribe的四个命令。
* 新开启的订阅客户端，无法收到该频道之前的消息，因为Redis不会对 发布的消息进行持久化。

{% hint style="info" %}
和很多专业的消息队列系统（例如Kafka、RocketMQ）相比，Redis的发 布订阅略显粗糙，例如无法实现消息堆积和回溯。但胜在足够简单，如果当前场景可以容忍的这些缺点，也不失为一个不错的选择。
{% endhint %}

### 取消订阅

```text
unsubscribe [channel [channel ...]]
```

客户端可以通过unsubscribe命令取消对指定频道的订阅，取消成功后， 不会再收到该频道的发布消息：

```text
127.0.0.1:6379> unsubscribe channel:sports
1) "unsubscribe"
2) "channel:sports"
3) (integer) 0
```

### 按照模式订阅和取消订阅

```text
psubscribe pattern [pattern...]
punsubscribe [pattern [pattern ...]]
```

除了subcribe和unsubscribe命令，Redis命令还支持glob风格的订阅命令 psubscribe和取消订阅命令punsubscribe，例如下面操作订阅以it开头的所有频道：

```text
127.0.0.1:6379> psubscribe it*
Reading messages... (press Ctrl-C to quit)
1) "psubscribe"
2) "it*"
3) (integer) 1
```

### 查询订阅

#### （1）查看活跃的频道

```text
pubsub channels [pattern]
```

所谓活跃的频道是指当前频道至少有一个订阅者，其中\[pattern\]是可以指定具体的模式：

```text
127.0.0.1:6379> pubsub channels
1) "channel:sports"
2) "channel:it"
3) "channel:travel"

127.0.0.1:6379> pubsub channels channel:*r*
1) "channel:sports"
2) "channel:travel"
```

#### （2）查看频道订阅数

```text
pubsub numsub [channel ...]
```

当前channel:sports频道的订阅数为2：

```text
127.0.0.1:6379> pubsub numsub channel:sports
1) "channel:sports"
2) (integer) 2
```

#### （3）查看模式订阅数

```text
pubsub numpat
```

当前只有一个客户端通过模式来订阅：

```text
127.0.0.1:6379> pubsub numpat
(integer) 1
```

