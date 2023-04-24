# 前言

> 大家可以在此按章节, 每个 proposal 一个单独的说明, 内容和大体格式参考[官方PDF](https://github.com/apache/incubator-hugegraph/files/11208510/diff_mix.pdf)说明
> 例如:
![image](https://user-images.githubusercontent.com/17706099/233821125-31ae6d1a-7ec1-4d20-90d6-9945eea88739.png)


### 1. Spark Connector (toolchain)
- 描述：

   关于 Spark 的集成，目前 hugegraph 社区有 SparkLoader 的实现，但是目前该实现较基础，缺乏批量生成 SST 文件功能，并且对于 spark 开发者而言将 dataframe 写入 hugegraph，需要自己基于 hugegraph-client 实现写数据逻辑，使用成本较高。为了能增加 hugegraph 在大数据处理场景的适用性，更好地与大数据生态集成，本提案需要实现基于 Spark datasource v2 实现通用的 hugegraph-spark-connector，并以此优化现有 SparkLoader。
- 产出标准：
    1. hugegraph-client 支持生成 sst 文件。
    2. 基于 spark datasource v2 从0到1的实现 hugegraph-spark-connector
        - 支持通过 server 进行批量点、边、属性数据写入。
        - 支持批量生成 RocksDB SST 文件。
    3. SparkLoader 基于 spark-connector 的重构。
    4. 完成相关单元测试 UT。
    5. 完成用户使用文档撰写。
- 技术要求： 
    1. 熟悉 Spark/Flink 环境或相关使用
    2. 熟悉图数据库, 熟悉 hugegraph 更佳
    3. 具备 java/scala 研发能力, 熟悉 Linux 基本使用

难度: 进阶

发起人: @simon824  (重要紧急)


### 2. Computer support loading from vertex/edge snapshot

- 描述：

   目前 hugegraph-computer 每次启动一个算法任务时，将会在 `k8s` 启动一个 `master` 节点和多个 `worker` 节点的计算集群，并将 `hugegraph-server` 中的图数据进行分片并加载到各 worker 节点上--我们称之为“数据输入”，当数据输入完成后才开始并行计算。这个数据输入的流程是非常耗时的，当对同一个图执行不同算法/任务时，均需要再走一遍数据输入流程。为了优化数据输入流程，我们可以将第一次加载好的数据（vertex/edge）生成 snapshot 快照保存起来，在执行其他算法时 `worker` 可以直接加载 snapshot，从而实现一次加载多次执行，这将大大的提高 computer 的执行效率。

- 产出标准：
   1. 实现 vertex/edge 生成 snapshot 保存到 k8s的 Persistent Volume，并将图的 metadata 保存到 etcd 中。
   2. 支持 computer 直接从 k8s 的 Persistent Volume 中恢复数据 vertex/edge 快照，并跳过数据分片和从 `hugegraph-server` 中拉取数据的过程。
   3. 完成 k8s operator 和 API 调用的相关适配。
   4. 完成相关单元测试 UT 和 CI。

- 技术要求：
   1. 熟悉分布式计算（图计算更佳）。
   2. 熟悉图数据库, 熟悉 hugegraph 更佳, 或熟悉 k8s 相关使用知识。
   3. 具备 java 研发能力, 熟悉 Linux 基本使用。

难度: 进阶

发起人: @coderzc (重要不紧急)

### 3. server 内存管理

- 内存池自己管理内存
- 控制任务资源使用

发起人: @javeme  (重要不紧急, 难度较高, 工作量大)

研发需要多久

### 4. 性能优化

- 改进原生并行集合 primitive map/set + 实现
- 实现基于 CBO 的查询优化
- 实现图计算框架的向量化计算优化

### 5. Go/Xx Client

> 需求度? 不同语言 discussion 投票 (必要不紧急)

PS: python client 已经在路上

### 6. 分布式存储适配与改进

- 描述:

   随着 HugeGraph 分布式存储的演进（RocksDB-based Multi-Raft），HugeServer 逐渐演变为无状态节点。而存储集群主要由 PD (Placement Driver) 和 StoreNode 来完成。PD 作为整个集群的管理调度节点，提供了服务注册、元数据存储与监听、分区管理等功能。存储集群会由若干个存储节点 StoreNode 组成，提供了按分区写入和读取数据的能力，并且支持水平扩展。本提案需要实现HugeServer 与 Pd、StoreNode 的集成。

- 产出标准:
   1. HugeGraph-Server + Hubble 实现向 PD 的服务注册。
   2. 图相关的元数据信息、任务信息存储到 PD 中，并且根据需要实现对 Key 的监听。
   3. 进行分布式后端存储的适配，实现数据的写入与查询。
     - 使用切边法，实现按顶点、出边、入边和索引按分区写入存储后端。
     - 实现按顶点或边ID到对应的StoreNode中查询数据。
   4. 完善相关的单元测试、使用文档、设计文档。

- 技术要求:
  1. 熟悉微服务场景下的服务注册与发现, 熟悉 KV 存储更佳。
  2. 熟悉分布式存储, 熟悉 HugeGraph-Server 代码更佳。
  3. 具备 java 研发能力。

难度: 进阶