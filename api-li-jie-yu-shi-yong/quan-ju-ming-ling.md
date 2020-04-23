# 全局命令1

### 1. 查看所有键

我们先往数据库中插入几个数据

```text
set hello world
set what redis
```

```text
keys *
```

![](../.gitbook/assets/image%20%2889%29.png)

### 2. 键总数

```text
dbsize
```

dbsize命令会返回当前数据库中键的总数。例如当前数据库有4个键， 分别是hello、what所以dbsize的结果是2

![](../.gitbook/assets/image%20%2899%29.png)

dbsize命令在计算键总数时不会遍历所有键，而是直接获取Redis内置的 键总数变量，所以dbsize命令的时间复杂度是O\(1\)。而keys命令会遍历所 有键，所以它的时间复杂度是O\(n\)，当Redis保存了大量键时，线上环境禁止使用。

### 3. 检查键是否存在

```text
exists key [key ...]
```

如果键存在则返回1，不存在则返回0：

![](../.gitbook/assets/image%20%2827%29.png)

### 4. 删除键

```text
del key [key ...]
```

del是一个通用命令，无论值是什么数据结构类型，del命令都可以将其 删除，例如下面将字符串类型的键hello删除：

![](../.gitbook/assets/image%20%2837%29.png)

返回结果为成功删除键的个数，假设删除一个不存在的键，就会返回0

### 5. 键过期

```text
expire key seconds
```

Redis支持对键添加过期时间，当超过过期时间后，会自动删除键，例如为键hello设置了10秒过期时间：

```text
set hello world
expire hello 10
```

ttl命令会返回键的剩余过期时间，它有3种返回值：

* 大于等于0的整数：键剩余的过期时间。
* -1：键没设置过期时间。
* ·-2：键不存在

![](../.gitbook/assets/image%20%2830%29.png)

#### 注意

* 如果expire key的键不存在，返回结果为0
* 如果过期时间为负值，键会立即被删除，犹如使用del命令一样
* persist命令可以将键的过期时间清除
* 对于字符串类型键，执行set命令会去掉过期时间，这个问题很容易 在开发中被忽视。
* Redis不支持二级数据结构（例如哈希、列表）内部元素的过期功

  能，例如不能对列表类型的一个元素做过期时间设置。

### 6. 键的数据结构类型

```text
type key
```

例如键hello是字符串类型，返回结果为string。键mylist是列表类型，返回结果为list：

![](../.gitbook/assets/image%20%283%29.png)

### 8. 键重命名

```text
rename key newkey
```

例如现有一个键值对，键为python，值为jedis：

![](../.gitbook/assets/image%20%285%29.png)

