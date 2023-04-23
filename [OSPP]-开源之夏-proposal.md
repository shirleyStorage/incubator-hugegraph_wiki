# 前言

> 大家可以在此按章节, 每个 proposal 一个单独的说明, 内容和大体格式参考[官方PDF](https://github.com/apache/incubator-hugegraph/files/11208510/diff_mix.pdf)说明
> 例如:
![image](https://user-images.githubusercontent.com/17706099/233821125-31ae6d1a-7ec1-4d20-90d6-9945eea88739.png)


### 1. Spark Connector (toolchain)
描述： 关于Spark的集成，目前 hugegraph 社区有 SparkLoader 的实现，但是目前该实现较基础，缺乏批量生成 sst 文件功能，并且对于 spark 开发者而言将 dataframe 写入 hugegraph，需要自己基于 hugegraph-client 实现写数据逻辑，使用成本较高。为了能增加 hugegraph 在大数据处理场景的适用性，更好地与大数据生态集成，本提案需要实现基于 spark datasourceV2 实现通用的 hugegraph-spark-connector。

对于 Spark 应用程序中，需要讲 dataframe 写入hugegraph

没有提供 spark connector 连接器，缺乏大数据的流/批处理能力，connector 能增加 hugegraph 在大数据处理场景的适用性，更好地与大数据生态集成。
产出标准：
1. hugegraph-client 支持生成 sst 文件。
2. 基于 spark datasourceV2 0到1实现 hugegraph-spark-connector
 - 支持通过 server 进行批量点、边、属性数据写入。
 - 支持批量生成 sst 文件。
3. SparkLoader 基于 spark-connector 的重构。
4. 完成相关UT。
5. 完成用户使用文档撰写。

技术要求： 熟悉Spark/Flink/hugegraph，具备java和scala研发能力。

发起人: @simon824  (重要紧急)


### 2. Computer support load vertex/edge snapshot

- 描述：
目前 hugegraph-computer 每次启动一个算法任务将会在 `k8s` 启动一个 `master` 节点和多个 `worker` 节点并将 `hugegraph-server` 中的图数据进行分片并加载到各 worker 节点上，然后开始并行计算，这个数据分片和加载的流程是非常耗时的，当对同一个图执行不用算法任务时需要再走一遍数据分片加载流程，我们可以将 vertex/edge 生成 snapshot 保存起来，在执行其他算法时 `worker` 可以直接加载 snapshot 来实现一次加载多次执行，这将大大的提高 computer 的执行效率。
- 产出标准：
   1. 实现 vertex/edge 生成 snapshot 保存到 k8s的 Persistent Volume 并将图的 metadata 保存到 etcd 中。
   2. 使 computer 支持直接从 k8s的 Persistent Volume 中 recovery vertex/edge snapshot，并跳过数据分片和从 `hugegraph-server` 中拉取数据的过程。
   3. 完成 k8s operator 和 API 调用的相关适配。
   4. 完成相关UT和CI。
- 技术要求：
熟悉分布式计算（图计算更佳）和k8s相关理论知识，具备java研发能力。

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

### 6. 分布式存储?

- 适配PD?
- 改进Store?