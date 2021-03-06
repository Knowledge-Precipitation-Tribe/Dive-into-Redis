# 简单动态字符串

在Redis中没有直接使用C语言传统的字符串表示，而是自己构建了一种名为简单动态字符串\(simple dynamic string, SDS\)的抽象类型，并将SDS用作Redis的默认字符串表示。

当Redis需要的不仅仅是一个字符串字面量，而是一个可以被修改的字符串时，Redis就会使用SDS来表示字符串值，比如在Redis的数据库里面，包含字符串值的键值对在底层都是由SDS实现的。

## SDS的定义

![](../.gitbook/assets/image%20%28241%29.png)

SDS遵循在C语言中字符串以空字符结尾的惯例，保存空字符的一字节空间不计算在SDS的len属性中，而且为空字符自动分配一字节空间，以及添加空字符到字符串末尾等操作都是SDS函数自动完成的，遵循这一惯例的好处就是SDS可以直接重用一部分C字符串的一些函数。

## SDS与C语言字符串的区别

从数据结构的定义上来看，sds就是将c语言字符串做了一下封装，那么为什么SDS更适合与在redis中存储字符串呢？主要有以下几方面的因素

### 常数时间获取字符串长度

在C语言中，字符串自身并不记录其长度信息，如果想要获取字符串的长度必须要遍历字符串才能得到其长度信息，而SDS结构体中默认记录了字符串的长度，所以对于字符串长度的获取是常数时间的。

### 杜绝缓冲区溢出

![](../.gitbook/assets/image%20%28239%29.png)

与C语言字符串不同，SDS完全杜绝了发生缓冲区溢出的可能性：当SDS需要修改时，API会自动查询SDS的空间是否满足修改所需要求，如果不满足的话，API会自动将SDS拓展至所需大小再进行修改，所以就不会出现上文中提到的缓冲区溢出问题。

### 减少修改字符串时带来的内存重分配次数

在C语言中每次增长或缩短一个字符串，都需要对内存进行重新分配，因为内存重分配是比较复杂的算法，而且还涉及系统调用，所以一般是一个比较耗时的操作，SDS为了避免这以情况的发生就通过声明未使用空间来进行操作，其中free就是用来记录未使用空间大小的，通过未使用空间SDS实现了空间预分配和惰性空间释放两种策略。

#### 空间预分配

当SDS的API对一个SDS进行修改，并且需要对SDS进行空间拓展的时候，程序不仅会为SDS分配修改所必须要的空间，还会为SDS分配额外的未使用空间。其中额外分配的未使用空间数量由以下公示决定：

* 如果对SDS进行修改之后，SDS的长度\(也就是len属性\)小于1MB，那么程序分配和len属性同样大小的未使用空间，此时len与free值相同。
* 如果对SDS进行修改之后，SDS的长度大于1MB，那么程序会分配1MB的未使用空间。

#### 惰性空间释放

惰性空间释放用于优化字符串的缩短操作：当SDS的API需要缩短SDS保存的字符串时，程序不立即使用内存重分配来回收缩短后多出来的字节，而是使用free属性将这些字节的数量记录起来，并等待将来使用。

## 二进制安全

在C语言中字符串必须复合某种编码如ASCII，并且除了字符串的末尾之外字符串中不能包含空字符串，否则最先被程序读入的空字符串将被误认为是字符串的结尾，这些限制是的C语言字符串只能保存文本数据，而不能保存像图像，音频，视频这类的二进制数据。

但是在SDS中，他是使用len属性来判断字符串长度的，所以任何二进制字符串都可以原样进行保存。

