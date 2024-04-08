# 前置条件

- Linux, MacOS (Windows 环境下尚未进行过测试)
- Java version ≥ 11
- Maven version ≥ 3.5.0

# 从源码构建并启动

1. clone 源码 pd-store 分支

```Bash
git clone -b pd-store https://github.com/apache/hugegraph.git
```

2. 在项目根目录下构建

```Bash
cd hugegraph
mvn clean install -DskipTests=true
```

若构建成功，PD, Store, Server 三个模块的构建产物将依次存放在

- PD: `hugegraph-pd/dist/hugegraph-pd-1.5.0.1`
- Store: `hugegraph-store/dist/hugegraph-store-1.5.0.1`
- Server: `hugegraph-server/apache-hugegraph-incubating-1.5.0.1`

```Bash
.
├── hugegraph-pd
│  └── dist
      ├── hugegraph-pd-1.5.0.1
      └── hugegraph-pd-1.5.0.1.tar.gz
├── hugegraph-server
│  ├── apache-hugegraph-incubating-1.5.0.1
│  └── apache-hugegraph-incubating-1.5.0.1.tar.gz
└─── hugegraph-store
   └── dist
      ├── hugegraph-store-1.5.0.1
      └── hugegraph-store-1.5.0.1.tar.gz
```

在运行 pd, store, server 时，上述路径即工作路径

3. 运行 pd

```Bash
cd hugegraph-pd/dist/hugegraph-pd-1.5.0.1
./bin/start-hugegraph-pd.sh
```

若启动正常，可以在 `hugegraph-pd/dist/hugegraph-pd-1.5.0.1/logs/hugegraph-pd.log` 找到下述日志

```Plain
2024-04-08 15:15:45 [main] [INFO] o.a.h.p.b.HugePDServer - Started HugePDServer in 3.879 seconds (JVM running for 5.149)
```

4. 运行 store

```Bash
cd hugegraph-store/dist/hugegraph-store-1.5.0.1
./bin/start-hugegraph-store.sh
```

若启动正常，可以在 `hugegraph-store/dist/hugegraph-store-1.5.0.1/logs/hugegraph-store.log` 找到下述日志

```Plain
2024-04-08 15:16:29 [main] [INFO] o.a.h.s.n.StoreNodeApplication - Started StoreNodeApplication in 4.794 seconds (JVM running for 6.21)
```

5. 运行 server

```Bash
cd hugegraph-server/apache-hugegraph-incubating-1.5.0.1
./bin/start-hugegraph.sh -p true
```

传入 `-p true` 导入示例图，参考 https://hugegraph.apache.org/docs/quickstart/hugegraph-server/#517-create-an-example-graph-when-startup

简单的验证

```Bash
> curl http://localhost:8080/graphs
{"graphs":["hugegraph"]}
```

6. 停止 server, store, pd

**按顺序**在上述**各目录下**执行

```Bash
./bin/stop-hugegraph.sh
./bin/stop-hugegraph-store.sh
./bin/stop-hugegraph-pd.sh
```

# 下载 tar 包启动

在这个[页面](https://github.com/hugegraph/hugegraph/releases/tag/pd-store-tmp)下载 pd, store, server tar 包并解压，解压之后将得到下述文件夹

- `hugegraph-pd-1.5.0.1`
- `hugegraph-store-1.5.0.1`
- `apache-hugegraph-incubating-1.5.0.1`

之后流程与上述一致

# 多节点配置参考

- 3 PD
  - raft ports: 8610, 8611, 8612
  - rpc ports: 8686, 8687, 8688
  - rest ports: 8620, 8621, 8622
- 3 Store
  - raft ports: 8610, 8611, 8612
  - rpc ports: 8686, 8687, 8688
  - rest ports: 8620, 8621, 8622
- 3 Server (disable auth + use distributed scheduler)
  - rest ports: 8081, 8082, 8083
  - rpc ports: 8091, 8092, 8093
  - gremlin ports: 8181, 8182, 8183

## server

`hugegraph.properties` 均为

```Properties
backend=hstore
serializer=binary
task.scheduler_type=distributed

# pd 服务地址，多个 pd 地址用逗号分割 ⚠️配置 pd 的 rpc 端口
pd.peers=127.0.0.1:8686,127.0.0.1:8687,127.0.0.1:8688
```

`rest-server.properties` 分别为

```Properties
restserver.url=http://127.0.0.1:8081
gremlinserver.url=http://127.0.0.1:8181

rpc.server_host=127.0.0.1
rpc.server_port=8091

server.id=server-1
server.role=master
restserver.url=http://127.0.0.1:8082
gremlinserver.url=http://127.0.0.1:8182

rpc.server_host=127.0.0.1
rpc.server_port=8092

server.id=server-2
server.role=worker
restserver.url=http://127.0.0.1:8083
gremlinserver.url=http://127.0.0.1:8183

rpc.server_host=127.0.0.1
rpc.server_port=8093

server.id=server-3
server.role=worker
```

`gremlin-server.yaml` 分别为

```Properties
host: 127.0.0.1
port: 8181
host: 127.0.0.1
port: 8182
host: 127.0.0.1
port: 8183
```

这个[压缩包](https://github.com/hugegraph/hugegraph/releases/download/pd-store-tmp/multi-server.zip)包含了 Server 节点 A, B, C 完整的配置文件，用于**在 IDEA 中**快捷启动多节点 Server

> 将 IDEA 某个运行配置的工作路径设置为对应的配置文件路径
>
> ![image](https://github.com/apache/incubator-hugegraph/assets/79143929/1abb5e66-1025-438c-b722-66b9abb4171a)

## pd

仅以 PD 节点 A 为例

修改配置文件 `hugegraph-pd-1.5.0.1/conf/application.yml`

```YAML
spring:
  application:
    name: hugegraph-pd

management:
  metrics:
    export:
      prometheus:
        enabled: true
  endpoints:
    web:
      exposure:
        include: "*"

logging:
  config: 'file:./conf/log4j2.xml'
license:
  verify-path: ./conf/verify-license.json
  license-path: ./conf/hugegraph.license
grpc:
  # 集群模式
  port: 8686 # ⚠️本地测试要配不同端口
  host: 127.0.0.1 

server:
  # rest 服务端口号
  port: 8620 # ⚠️本地测试要配不同端口

pd:
  # 存储路径
  data-path: ./pd_data # ⚠️本地测试要配不同路径
  # 自动扩容的检查周期，定时检查每个 store 的分区数量，自动进行分区数量平衡
  patrol-interval: 1800
  # 初始 store 列表，在列表内的 store 自动激活
  initial-store-count: 1
  # grpc IP:grpc port 
  # store 的配置信息
  initial-store-list: 127.0.0.1:8500,127.0.0.1:8501,127.0.0.1:8502

raft:
  # 集群模式
  address: 127.0.0.1:8610 # ⚠️本地测试要配不同端口
  peers-list: 127.0.0.1:8610,127.0.0.1:8611,127.0.0.1:8612

store:
  # store 下线时间。超过该时间，认为 store 永久不可用，分配副本到其他机器，单位秒
  max-down-time: 172800
  # 是否开启 store 监控数据存储
  monitor_data_enabled: true
  # 监控数据的间隔，minute (默认), hour, second
  # default: 1 min * 1 day = 1440
  monitor_data_interval: 1 minute
  # 监控数据的保留时间 1 天; day, month, year
  monitor_data_retention: 1 day
  initial-store-count: 1

partition:
  # 默认每个分区副本数
  default-shard-count: 1
  # 默认每机器最大副本数，初始分区数 = store-max-shard-count * store-number / default-shard-count
  store-max-shard-count: 12
```

这个[压缩包](https://github.com/hugegraph/hugegraph/releases/download/pd-store-tmp/multi-pd.zip)包含了 PD 节点 A, B, C 完整的配置文件，用于**在 IDEA 中**快捷启动多节点 PD

## store

仅以 Store 节点 A 为例

修改配置文件 `hugegraph-store/dist/hugegraph-store-1.5.0.1/conf/application.yml`

```YAML
pdserver:
  # pd 服务地址，多个 pd 地址用逗号分割 ⚠️配置 pd 的 rpc 端口
  address: 127.0.0.1:8686,127.0.0.1:8687,127.0.0.1:8688

management:
  metrics:
    export:
      prometheus:
        enabled: true
  endpoints:
    web:
      exposure:
        include: "*"

grpc:
  # grpc 的服务地址
  host: 127.0.0.1
  port: 8500 # ⚠️本地测试要配不同端口
  netty-server:
    max-inbound-message-size: 1000MB
raft:
  # raft 缓存队列大小
  disruptorBufferSize: 1024
  address: 127.0.0.1:8510 # ⚠️本地测试要配不同端口
  max-log-file-size: 600000000000
  # 快照生成时间间隔，单位秒
  snapshotInterval: 1800
server:
  # rest 服务地址
  port: 8520 # ⚠️本地测试要配不同端口

app:
  # 存储路径，支持多个路径，逗号分割
  data-path: ./storage # ⚠️本地测试要配不同路径
  #raft-path: ./storage

spring:
  application:
    name: store-node-grpc-server
  profiles:
    active: default
    include: pd

logging:
  config: 'file:./conf/log4j2.xml'
  level:
    root: info
```

这个[压缩包](https://github.com/hugegraph/hugegraph/releases/download/pd-store-tmp/multi-store.zip)包含了 Store 节点 A, B, C 完整的配置文件，用于**在 IDEA 中**快捷启动多节点 Store