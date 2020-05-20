# Redis监控运维云平台CacheCloud

无论使用还是运维Redis，千万不要将其看作黑盒，虽然Redis提供了一些命令来做监控统计（例如info）和日常运维（例如redis-trib.rb），但是当 Redis达到了一定规模，这些命令会变得捉襟见肘，如果通过平台化的工具统一监控和管理将极大地提升开发和运维人员工作效率。本章首先分析Redis监控和运维中现有的问题，随后将介绍开源的Redis私有云平台CacheCloud，及其解决这些问题的方案。主要内容如下：

* 由Redis监控和运维的现有问题引出CacheCloud。
* 快速部署：快速搭建CacheCloud项目。
* 机器部署：实现CacheCloud对机器管理部署。
* 接入应用：使用CacheCloud部署Redis Cluster并完成客户端快速接入。
* 用户功能：站在开发人员角度介绍CacheCloud相关功能。
* 运维功能：站在运维人员角度介绍CacheCloud相关功能。
* 客户端上报：CacheCloud获取上报客户端统计信息。

