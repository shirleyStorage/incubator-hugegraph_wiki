# 前言

> 大家可以在此按章节, 每个 proposal 一个单独的说明, 内容和大体格式参考[官方PDF](https://github.com/apache/incubator-hugegraph/files/11208510/diff_mix.pdf)说明
> 例如:
![image](https://user-images.githubusercontent.com/17706099/233821125-31ae6d1a-7ec1-4d20-90d6-9945eea88739.png)


### 1. Spark&Flink Write Connector (toolchain)
- 描述：
目前 hugegraph 社区没有提供 spark/flink connector 连接器，缺乏大数据的流/批处理能力，connector 能增加 hugegraph 在大数据处理场景的适用性，更好地与大数据生态集成。
- 产出标准：
   1. 实现 hugegraph spark writer，支持 hugegrpah 点边属性的数据写入。
   2. 实现 hugegraph flink writer，支持 hugegrpah 点边属性的数据写入。
   3. 完成使用文档撰写。
   4. 完成相关UT和CI。
- 技术要求：
熟悉Spark/Flink/hugegraph，具备java和scala研发能力。

发起人: @simon824  (重要紧急)


### 2. Computer support load vertex/edge snapshot

- 代码合并
- xx

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