# GEO

Redis3.2版本提供了GEO（地理信息定位）功能，支持存储地理位置信息用来实现诸如附近位置、摇一摇这类依赖于地理位置信息的功能，对于需要实现这些功能的开发者来说是一大福音。

### 增加地理位置信息

```text
geoadd key longitude latitude member [longitude latitude member ...]
```

longitude、latitude、member分别是该地理位置的经度、纬度、成员，下表展示5个城市的经纬度。

![](../.gitbook/assets/image%20%2884%29.png)

cities:locations是上面5个城市地理位置信息的集合，现向其添加北京的地理位置信息：

```text
127.0.0.1:6379> geoadd cities:locations 116.28 39.55 beijing
(integer) 1
```

返回结果代表添加成功的个数，如果cities:locations没有包含beijing，那么返回结果为1，如果已经存在则返回0：

```text
127.0.0.1:6379> geoadd cities:locations 116.28 39.55 beijing
(integer) 0
```

如果需要更新地理位置信息，仍然可以使用geoadd命令，虽然返回结果为0。geoadd命令可以同时添加多个地理位置信息：

```text
127.0.0.1:6379> geoadd cities:locations 117.12 39.08 tianjin 114.29 38.02 shijiazhuang 118.01 39.38 tangshan 115.29 38.51 baoding
(integer) 4
```

### 获取地理位置信息

```text
geopos key member [member ...]
```

下面操作会获取天津的经维度：

```text
127.0.0.1:6379> geopos cities:locations tianjin
1) 1) "117.12000042200088501"
   2) "39.0800000535766543"
```

### 获取两个地理位置的距离

```text
geodist key member1 member2 [unit]
```

其中unit代表返回结果的单位，包含以下四种：

* m（meters）代表米。
* km（kilometers）代表公里。
* mi（miles）代表英里。
* ft（feet）代表尺。

下面操作用于计算天津到北京的距离，并以公里为单位：

```text
127.0.0.1:6379> geodist cities:locations tianjin beijing km
"89.2061"
```

### 删除地理位置信息

```text
zrem key member
```

GEO没有提供删除成员的命令，但是因为GEO的底层实现是zset，所以可以借用zrem命令实现对地理位置信息的删除。

