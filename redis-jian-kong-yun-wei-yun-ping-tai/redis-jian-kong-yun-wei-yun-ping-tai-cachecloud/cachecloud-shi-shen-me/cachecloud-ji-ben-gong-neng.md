# CacheCloud基本功能

CacheCloud它实现多种Redis类型（Redis Standalone、Redis Sentinel、 Redis Cluster）的自动部署、解决Redis节点碎片化现象，提供完善的统计、 监控、运维功能，减少运维成本和误操作，提高机器的利用率，提供灵活的 伸缩性，可方便地接入客户端，对于Redis的开发和运维人员非常有帮助。 整体功能架构如图所示。

![](../../../.gitbook/assets/image%20%28237%29.png)

CacheCloud提供的主要功能如下： 

* 监控统计：提供了机器、应用、实例下各个维度数据的监控和统计界 面。 
* 一键开启：Redis Standalone、Redis Sentinel、Redis Cluster三种类型的 应用，无需手动配置初始化。 
* Failover：支持Redis Sentinel、Redis Cluster的高可用模式。 
* 可伸缩性：提供完善的垂直和水平在线伸缩功能。 
* 完善运维：提供自动化运维功能，避免纯手工运维出错。 
* 方便的客户端：方便快捷的客户端接入，同时支持客户端性能统计。 
* 元数据管理：提供机器、应用、实例、用户信息管理。 
* 流程化：提供申请、运维、伸缩、修改等完善的处理流程。 
* 一键导入：一键导入已经存在的Redis。 ·迁移数据：Redis Standalone、Redis Sentinel、Redis Cluster、AOF、RDB可进行数据迁移。

