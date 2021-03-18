# 整数集合

整数集合是集合键的底层实现之一，当一个集合只包含整数值元素，并且这个集合的元素数量不多时，Redis就会使用整数集合作为集合键的底层实现。

## 整数集合的实现

整数集合是Redis用于保存整数值的集合抽象数据结构，它可以保存类型为int16\_t,int32_\__t,int64\_t的整数值，并且可以保证集合中不会出现重复的元素。

![](../.gitbook/assets/image%20%28252%29.png)

contents数组是整数集合的底层实现：整数集合的每个元素都是contents数组的一个数组项，各个项在数组中按照值的大小从小到大有序的排列，并且数组中不包含任何重复项。legth属性记录了整数集合包含的元素数量，即contents数组的长度。

![](../.gitbook/assets/image%20%28253%29.png)


