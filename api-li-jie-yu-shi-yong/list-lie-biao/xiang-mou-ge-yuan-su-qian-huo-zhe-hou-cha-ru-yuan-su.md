# 向某个元素前或者后插入元素

```text
linsert key before|after pivot value
```

linsert命令会从列表中找到等于pivot的元素，在其前（before）或者后 （after）插入一个新的元素value，例如下面操作会在列表的元素b前插入 java：

![](../../.gitbook/assets/image%20%2881%29.png)

可以看到插入是找到第一个元素为止。

