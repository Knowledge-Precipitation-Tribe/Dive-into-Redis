# 事务与Lua

为了保证多条命令组合的原子性，Redis提供了简单的事务功能以及集成Lua脚本来解决这个问题。本节首先简单介绍Redis中事务的使用方法以及它的局限性，之后重点介绍Lua语言的基本使用方法，以及如何将Redis和Lua脚本进行集成，最后给出Redis管理Lua脚本的相关命令。



