1. # 基于docker的redis sentinel操作手册

   ## 如何安装

   git clone https://github.com/ScottAlone/docker-redis-sentinel

   firewall-cmd --add-port 7001-7006/tcp

   cd docker-redis-sentinel

   docker-compose build

   docker-compose up

   （注：开放防火墙端口是为了在外部机器上连接）

   ​

   ## docker-compose.yml

   ```yaml
   version: '2'

   services:
     master:
       build:
         context: master
         dockerfile: Dockerfile
         args:
           PORT: 7001
       network_mode: "host"

     slave1:
       build:
         context: slave1
         dockerfile: Dockerfile
         args:
           PORT: 7002
       network_mode: "host"

     slave2:
       build:
         context: slave2
         dockerfile: Dockerfile
         args:
           PORT: 7003
       network_mode: "host"

     sentinel1:
       build: 
         context: sentinel1
         dockerfile: Dockerfile
         args:
           PORT: 7004
       network_mode: "host"

     sentinel2:
       build: 
         context: sentinel2
         dockerfile: Dockerfile
         args:
           PORT: 7005
       network_mode: "host"

     sentinel3:
       build:
         context: sentinel3
         dockerfile: Dockerfile
         args:
           PORT: 7006
       network_mode: "host"

   ```

   context指定每个镜像的```Dockerfile```所在位置。

   网络模式使用host，```PORT```参数用于配置redis实例在宿主机使用的端口。

   ​

   # 配置

   ### 选举的优先级

   配置文件：redis.conf

   参数：```slave-priority```

   含义：当主节点故障时，需要选举新的主节点，该值越小，则选举时优先级越高，默认为100。

   ​

   ### 哨兵的配置

   配置文件：sentinel.conf

   参数：```sentinel monitor mymaster 0.0.0.0 7001 2```

   含义：配置哨兵监视的主节点，```mymaster```为主节点名，```0.0.0.0```与```7001```分别表示主节点的ip和端口，```2```则表示在主节点故障时，需要两个哨兵同意才能切换主节点。如果需要外部机器访问，需将ip改为真实ip。

   参数：```sentinel down-after-milliseconds mymaster 5000```

   含义：```5000```表示在5000毫秒后sentinel标记主节点为主观下线。

   参数：```sentinel parallel-syncs mymaster 1```

   含义：指定了在执行故障转移时，最多可以有多少个从Redis实例在同步新的主实例。在从Redis实例较多的情况下这个数字越小，同步的时间越长，完成故障转移所需的时间就越长。

   参数：```sentinel failover-timeout mymaster 5000```

   含义：```5000```表示在5000毫秒后对主节点进行故障迁移。
