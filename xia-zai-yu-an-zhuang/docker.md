# Docker

对Docker不太熟悉的朋友可以看此文档：[动手学Docker](https://docs.docker.knowledge-precipitation.site/)

这里我们使用Docker-Compose的方式安装Redis



接下来使用命令启动Redis服务

```text
docker-compose up -d
```

接下来我们可以进入到容器当中

```text
docker-compose exec db bash
```

在容器中我们就可以对Redis进行各种操作了。

