# 快速部署

## CacheCloud环境需求

安装部署CacheCloud需要以下环境： 

* JDK7+：CacheCloud使用Java语言开发，并使用了JDK7的一些特性。 
* Maven3：CacheCloud使用Maven3作为开发构建工具。 
* MySQL5.5+：CacheCloud需要Redis的相关元信息进行持久化。 
* Redis：CacheCloud支持对2.8以上版本的Redis，但建议读者使用 Redis3.0+。

{% hint style="info" %}
上述JDK指的是Oracle JDK，如果是Open JDK会存在错误。 CacheCloud提供了视频教 程：[http://my.tv.sohu.com/pl/9100280/index.shtml。](http://my.tv.sohu.com/pl/9100280/index.shtml。)
{% endhint %}

## CacheCloud快速开始

### 1.下载项目源码

访问CacheCloud的GitHub主页，可以通过两种方式下载CacheCloud的源代码。

* 直接下载zip压缩包。
* 通过git选择对应的分支进行克隆。

master和各个release版本是生产可用的，其他分支可能是处于开发阶段的，请慎重选择。

CacheCloud目录结构如下：

![](../.gitbook/assets/image%20%2827%29.png)

### 2.初始化数据库

在MySQL中创建数据库cache\_cloud（UTF-8编码），将 cachecloud/script/cachecloud.sql文件导入到MySQL，它是Cachecloud的表结 构。

### 3.CacheCloud项目配置

CacheCloud项目中的online.properties文件（cachecloud-openweb/src/main/swap目录下）中包含了MySQL的配置信息以及CacheCloud项目 的启动端口（CacheCloud可以看作是一个Web项目），如表所示。

![](../.gitbook/assets/image%20%2837%29.png)

上述配置只是CacheCloud的最简配置，当项目启动后可以在后台设置更多的参数，后面会进行介绍。

### 4.启动CacheCloud系统

#### （1）构建项目

在项目的根目录下运行如下Maven命令，该命令会进行项目的构建：

```bash
mvn clean compile install -Ponline
```

#### （2）启动项目

如果只是想调试或者使用开发工具（例如Eclipse）测试一下 CacheCloud，可以在项目的cachecloud-open-web模块下运行如下命令，启动 CacheCloud：

```bash
mvn spring-boot:run
```

如果想在Linux上使用生产环境部署CacheCloud，执行deploy.sh脚本 （cachecloud/script目录下）。

例如当前cachecloud根目录在/data下，执行如下操作即可：

```bash
sh deploy.sh /data
```

deploy.sh脚本会将编译后的CacheCloud工程包、配置、启动脚本拷贝到/opt/cachecloud-web目录下。

当一切准备好之后，可以执行sh/opt/cachecloud-web/start.sh来启动 CacheCloud：

```bash
sh /opt/cachecloud-web/start.sh
```

启动后可以执行如下操作观察启动日志：

```bash
tail –f /opt/cachecloud-web/logs/cachecloud-web.log
```

#### （3）登录确认

Cachecloud启动成功后，访问http://127.0.0.1:8585/，如果出如图所示的登录界面说明启动成功，使用默认用户名admin、密码admin登录系统即可。

![](../.gitbook/assets/image%20%2840%29.png)

{% hint style="info" %}
CacheCloud启动常见错误解决方法可以参考[http://cachecloud.github.io。](http://cachecloud.github.io。)
{% endhint %}

