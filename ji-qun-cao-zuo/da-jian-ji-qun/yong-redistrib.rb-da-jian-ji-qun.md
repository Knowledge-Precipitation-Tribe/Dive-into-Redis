# 用redis-trib.rb搭建集群

redis-trib.rb是采用Ruby实现的Redis集群管理工具。内部通过Cluster相关命令帮我们简化集群创建、检查、槽迁移和均衡等常见运维操作，使用之前需要安装Ruby依赖环境。下面介绍搭建集群的详细步骤。

## Ruby环境准备

安装Ruby：

```bash
-- 下载ruby
wget https:// cache.ruby-lang.org/pub/ruby/2.3/ruby-2.3.1.tar.gz
-- 安装ruby
tar xvf ruby-2.3.1.tar.gz 
./configure -prefix=/usr/local/ruby 
make 
make install 
cd /usr/local/ruby 
sudo cp bin/ruby /usr/local/bin 
sudo cp bin/gem /usr/local/bin
```

安装rubygem redis依赖：

```bash
wget http:// rubygems.org/downloads/redis-3.3.0.gem 
gem install -l redis-3.3.0.gem 
gem list --check redis gem
```

安装redis-trib.rb：

```bash
sudo cp /{redis_home}/src/redis-trib.rb /usr/local/bin
```

安装完Ruby环境后，执行redis-trib.rb命令确认环境是否正确，输出如下：

```bash
# redis-trib.rb
```

![](../../.gitbook/assets/image%20%28209%29.png)

从redis-trib.rb的提示信息可以看出，它提供了集群创建、检查、修复、 均衡等命令行工具。这里我们关注集群创建命令，使用redis-trib.rb create命令可快速搭建集群。

## 准备节点

首先我们跟之前内容一样准备好节点配置并启动：

```bash
redis-server conf/redis-6481.conf 
redis-server conf/redis-6482.conf 
redis-server conf/redis-6483.conf 
redis-server conf/redis-6484.conf 
redis-server conf/redis-6485.conf 
redis-server conf/redis-6486.conf
```

## 创建集群

启动好6个节点之后，使用redis-trib.rb create命令完成节点握手和槽分配 过程，命令如下：

```bash
redis-trib.rb create --replicas 1 127.0.0.1:6481 127.0.0.1:6482 127.0.0.1:6483 127.0.0.1:6484 127.0.0.1:6485 127.0.0.1:6486
```

--replicas参数指定集群中每个主节点配备几个从节点，这里设置为1。我们出于测试目的使用本地IP地址127.0.0.1，如果部署节点使用不同的IP地 址，redis-trib.rb会尽可能保证主从节点不分配在同一机器下，因此会重新排 序节点列表顺序。节点列表顺序用于确定主从角色，先主节点之后是从节 点。创建过程中首先会给出主从节点角色分配的计划，如下所示。

![](../../.gitbook/assets/image%20%28204%29.png)

当我们同意这份计划之后输入yes，redis-trib.rb开始执行节点握手和槽 分配操作，输出如下：

![](../../.gitbook/assets/image%20%2829%29.png)

最后的输出报告说明：16384个槽全部被分配，集群创建成功。这里需 要注意给redis-trib.rb的节点地址必须是不包含任何槽/数据的节点，否则会 拒绝创建集群。

## 集群完整性检查

集群完整性指所有的槽都分配到存活的主节点上，只要16384个槽中有 一个没有分配给节点则表示集群不完整。可以使用redis-trib.rb check命令检 测之前创建的两个集群是否成功，check命令只需要给出集群中任意一个节 点地址就可以完成整个集群的检查工作，命令如下：

```bash
redis-trib.rb check 127.0.0.1:6379 
redis-trib.rb check 127.0.0.1:6481
```

当最后输出如下信息，提示集群所有的槽都已分配到节点：

```bash
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage... 
[OK] All 16384 slots covered.
```

