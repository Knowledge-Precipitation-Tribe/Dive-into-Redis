# 阻塞操作

阻塞式弹出如下：

```text
blpop key [key ...] timeout
brpop key [key ...] timeout
```

blpop和brpop是lpop和rpop的阻塞版本，它们除了弹出方向不同，使用 方法基本相同，所以下面以brpop命令进行说明，brpop命令包含两个参数：

* key\[key...\]: 多个列表的键。
* timeout: 阻塞时间（单位: 秒）

