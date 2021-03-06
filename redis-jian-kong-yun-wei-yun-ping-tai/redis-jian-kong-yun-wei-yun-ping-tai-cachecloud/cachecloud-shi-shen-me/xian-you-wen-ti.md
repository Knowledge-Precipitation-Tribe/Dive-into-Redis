# 现有问题

## 1.部署成本

我们在之前详细讲解了Redis Sentinel和Redis Cluster的安装、 配置、部署、运维。以Redis Cluster为例子，虽然Redis的作者开发了redistrib.rb这样的工具帮助我们快速构建和管理Redis Cluster，但是每个Redis节 点仍然需要手工配置和启动，相对来说还是比较繁琐的，而且由于是人工操 作，所以存在一定的错误率。例如作为一个Redis运维人员，管理几百上千 个Redis节点是很正常的事，如果单纯手工安装配置，既耗时又容易出错。

## 2.实例碎片化

关系型数据库（例如Oracle、MySQL）发展很多年已经非常成熟，会有 专职的DBA人员管理，运维流程和监控平台相对成熟稳定。对于像Redis这 样的NoSQL数据库，很多公司没有专职人员来维护，于是就会出现一种现 象：Redis由各个业务组来维护，造成Redis散落在各个机器上，没有整体的 管理。并且存在着很多由于业务收缩或者下线无人管理的Redis节点。高效 的做法应该是提供统一管理和监控的Redis平台，用于管理机器、集群、节 点、用户等资源并做好全方位监控，防止各种“私搭乱建”造成的混乱现象。

## 3.监控、统计和管理不完善

Redis Live等工具虽然提供了可视化的方式来监控Redis的相关数据， 但是如果从功能全面性上还是不够的，例如Redis2.8之后提供的Redis Sentinel和Redis3.0提供的Redis Cluster，目前的开源工具没有提供较好的支持，而且对于Redis info中的某些重要指标也没有实现很好的监控和报警功 能。

## 4.运维、经济成本

业务组运维Redis会造成如下三个问题：

* 业务组的开发人员可能更加善于使用Redis实现各种功能，但是没有足 够的精力和经验来维护好Redis。
* 各个业务组的Redis较为分散地部署在各自服务器上，造成机器利用率 较低，出现大量闲置资源，同时监控和运维无法有效支撑。
* 各个业务组的Redis使用各种不同的版本，不便于管理和交互。

所以，应该由一些在Redis运维方面更有经验的人来维护，使得开发者 更加关注于Redis使用本身，这样开发和运维可以各自做自己擅长的事情。

