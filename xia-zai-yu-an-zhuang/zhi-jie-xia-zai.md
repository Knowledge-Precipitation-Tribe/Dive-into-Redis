# 直接下载

## 下载

下载并解压redis压缩包，在linux中使用ln创建连接，这样直接使用redis操作即可

```text
wget http://download.redis.io/releases/redis-5.0.5.tar.gz
$ tar xzf redis-5.0.5.tar.gz
$ ln -s redis-5.0.5 redis
$ cd redis-5.0.5
```

之后使用命令对redis进行编译

```text
make
```

## 启动redis服务

```text
src/redis-server
```

## 客户端交互

您可以使用内置的客户端与Redis进行交互:

```text
src/redis-cli
```

