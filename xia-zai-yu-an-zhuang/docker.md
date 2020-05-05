# Docker

对Docker不太熟悉的朋友可以看此文档：[动手学Docker](https://docs.docker.knowledge-precipitation.site/)

这里我们使用Docker-Compose的方式安装Redis

{% code title="docker-compose.yml" %}
```text
version: "3"

services:
  redis:
    image: redis
    restart: always
    ports:
      - "6379:6379"
    volumes:
      - redis-db:/data

volumes:
  redis-db:
```
{% endcode %}

接下来使用命令启动Redis服务

```text
docker-compose up -d
```

接下来我们可以进入到容器当中

```text
docker-compose exec redis bash
```

进入容器中后我们就可以使用客户端连接来对Redis进行各种操作了。

```text
redis-cli
```

