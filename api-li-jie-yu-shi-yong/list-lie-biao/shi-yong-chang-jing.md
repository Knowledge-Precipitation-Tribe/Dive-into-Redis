# 使用场景

### 消息队列

Redis的lpush+brpop命令组合即可实现阻塞队列，生产者客户端使用lrpush从列表左侧插入元素，多个消费者客户端使用brpop命令阻塞式的“抢”列表尾部的元素，多个客户端保证了消费的负载均衡和高可用性。

![](../../.gitbook/assets/image%20%284%29.png)

### 文章列表

每个用户有属于自己的文章列表，现需要分页展示文章列表。此时可以考虑使用列表，因为列表不但是有序的，同时支持按照索引范围获取元素。

### 列表使用口诀

* lpush+lpop=Stack（栈） 
* lpush+rpop=Queue（队列） 
* lpsh+ltrim=Capped Collection（有限集合） 
* lpush+brpop=Message Queue（消息队列）

