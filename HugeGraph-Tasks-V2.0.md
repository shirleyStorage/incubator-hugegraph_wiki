> For Students & 开源之夏扩展的完整版本, 开源之夏原有任务不受影响, 这边是全新扩展出的更多任务, 随时更新ing
>
> **注:** 开始任务前, 请**务必**先使用 + 阅读一下已有的 HugeGraph 相关[文档](https://github.com/apache/incubator-hugegraph/issues/2212)/文章/Slide (参考[资料](https://github.com/apache/incubator-hugegraph-doc/issues/207)), 把主流程和背景大概清楚后, 再开始阅读源码

# 必读部分

下列任务基本都位于 [Server](github.com/apache/hugegraph) 主仓库 (github.com/apache/hugegraph), 基本都是核心的**数据结构/存储结构/性能优化/新增功能/分布式存储**这几块, 不同于开源之夏 [OSPP](https://summer-ospp.ac.cn/org/orgdetail/ec8b597b-248f-4592-bd1c-be22e52a5654?lang=zh) 列表已有的任务(且 OSPP 活动限定一个 task 仅可 1 人参与), **特点如下**:

1. 下面新增的所有任务(不包括进阶要求)都是有**大量**的已有**源码 + 设计文档**可参考的, 大幅降低了参与的门槛 + 难度/工作量 (文档会同步到 Wiki)
2. 给出了每个任务的评估难度/工作量等, 大家可根据个人情况自由选择(**1 ~ N**个, 无限制), 另建议每个任务 1 个同学参与 (组队至少按**子项**拆分)
3. 多数任务有**基础/进阶**两个部分, 完成任一部分皆可获得奖金, 更灵活
4. 在 OSPP 中**提交了任务/中选**的同学, 也可**同时**参与这部分新增任务 (前提是有余力)
5. 需要注意同样**需要提交**基本的 `proposal` 和**时间规划**/背景说明, 有类似的 task 结项要求 (proposal 可简洁清晰化)
6. 新增任务**报名截止日期**为 **7 月 1号**前, 鼓励大家提前准备/报名, 任务被提前**确认预订**后会及时更新状态为"**已预订**"

总体来说, 新增的任务是为了让更多的同学有机会参与开源/HugeGraph, 只是参与方式 + 任务量安排/门槛上更加灵活, 目前支持下面 2 种, 大家可以根据**个人倾向**自行选择:

**任务参与方式**:

1. [CCF协会-GLCC](https://www.gitlink.org.cn/glcc) 开源活动中参与模式 (限制是平台赞助项目需要在 CCF 专属的代码平台[类似 gitee]进行提交与审阅/同步等, 其它待确定, 自赞助不受影响)
2. Apache HugeGraph 基金会直接分配模式, 社区独立的任务模式 (无额外第三方平台, 灵活性最高)

注: 因与 OSPP 官方沟通后, 平台已经不允许再新增/修改任何 Task, 所以目前先考虑这两种方式, 有更好的方式或者平台提交任务可随时告知, 另外不确定 mentor/联系方式的任务可先联系 [imbajin](https://github.com/imbajin) 并加学生群接收最新消息

## 0. 分布式存储的 2.0 大融合

在之前开源之夏的分布式存储任务[基础](https://summer-ospp.ac.cn/org/prodetail/23ec80344?list=org&navpage=org)上, 诚邀熟悉`分布式存储/RocksDB/LSM` 的同学参与其他相关任务, 这里会持续更新拆分 (或是有意报名 OSPP 对应项目担心落选的同学也可联系报名)

**mentor**: [simon](https://github.com/simon824) / zyj

## 1. Server 图存储优化与元信息独立化

**背景:**

HugeGraph 之前的元信息是存储在第三方的存储组件中, 没有单独的元信息存储节点(PD), 现在会引入新的 PD 节点(**已有**), 我们需要做的是让当前的 Server 的元信息能(可选)存储于 PD 之上, 并优化之前冗余/非必要的表结构, 进行 `table struct` 的整合, 最后是对异步等 Server Task 的存储进行拆分和优化

**核心任务:**

1. Graph Schema 信息存储到元数据中心 (PD(Place Driver), 也可简单理解为 `meta-server`)

   **关键描述:** schema 等图的**元信息**的存储移植到元数据中心 PD 存储，后端存储不再使用m store存储 [依赖 PD]

2. 存储表结构优化缩减到 6 个 (原 10 +)

   **关键描述**: 移除`s store`更改表**映射**关系，减少为: `g+v、g+oe、g+ie、g+index、g+task、g+olap`

3. 任务存储结果拆分

   **关键描述:** 任务结果单独存储到 `task_result` 表中任务信息查询，只有查询单个任务时，才会 **merge** `task_result`表的结果信息

4. 分布式异步任务调度器

   **关键描述:** 能支持**多节点** server 运行时的任务(分布式)调度 [已有核心代码实现, 依赖 PD]

**技能要求:**

1. 熟练在 Java + Linux 下开发, 理解当前 HugeGraph 的基本图(点边)**存储结构**和设计实现 + **读写**流程
2. 了解分布式系统中 MetaServer/PD 的设计作用和基本通信方式, 熟悉 etcd/zookeeper/RPC 更佳
3. 需要有较强的**源码阅读** + 问题分析/解决能力 (任务已有**大量**源码实现可参考/复用, 亦有基本的设计文档)

**mentor**: [imbajin](https://github.com/imbajin)

**Difficulty:** middle (3 星⭐)

**Size**: middle (3.5 星⭐, 已有 **7成**源码实现)

**Bonus:** 8k/10k (核心/全部完成)

## 2. 边(Edge)的数据结构增强 (父子边)

**背景:**

HugeGraph 原本的边设计存储结构可参考已有[文档](https://github.com/apache/incubator-hugegraph-doc/issues/207), 那么之前的设计存在一些图定义上的不便之处, 例如 `likes` 作为一类 `EdgeLabel`, 只能唯一作用与两个 `VertexLabel` 之间(例如 `Teacher–like–>Student`), 然后其他的 `VertexLabel`只能选择单独新建其他名字标示 `likes`, 比如 (`Student –like2 –> Dog`), 从而边与边之间缺乏一种**继承关系**, 每个 `EdgeLabel` 都是孤立的, 在此背景下, 我们对 `EdgeLabel ` 的存储结构进行优化调整, 使其能支持"父子边 & 通用边" 的 2 个功能 (**进阶项:** 可以对 VertexLabel 也进行类似的思考与优化)

**核心任务:**

1. 边 Schema 支持"**父子边**"继承关系/功能

   **关键描述:** 边存储的 `rowkey` 结构增加**子边类型**,完成适配, 并思考新增了字段后产生的影响, 优化边的查询

2. 支持 general 边(类型)

   **关键描述**: 增加 general 边类型，定义 `schema` 时不再需要**指定**边的 `source/target` 的 `vertexlabel` 类型

**技能要求:**

1. 熟练在 Java + Linux 下开发, 理解当前 HugeGraph 的基本图(点边)**存储结构**和设计实现 + **读写**流程
2. 熟悉当前 HugeGraph 图点边存储的细节以及**利弊**点, 能结合需求思考图设计的考量点
3. 需要有较强的**源码阅读** + 问题分析能力 (任务已有**大量**源码实现可参考/复用, 亦有基本的设计文档)

**mentor**: cx/待定

**Difficulty:** middle (2.5星⭐)

**Size**: middle (3 星⭐, 已有 **8成**源码实现)

**Bonus:** 3k (完成进阶项则奖金至10k)

## 3/4. 图 OLTP 算法的增强与优化

**背景:**

了解了 HugeGraph 的读/查询流程之后, 可知道我们主要提供两种查询方式 :① Gremlin/Cypher 的通用图语言查询 ② 单独的 `RESTful` 图算法(例最短路径/K 步邻居/相似性等), ①和②各有利弊, 那么这个的任务是对 ② 的大量已有算法进行优化可功能上的扩展: 例如减少它灵活性上的不足, 在查询返回结果里允许自定义**过滤属性**/跳过不必要的搜索/提前停止等, 以此优化查询性能, 它的另一个进阶的任务是可使得 OLTP 算法都能并行去执行 (目前大部分是**串行/单线程**模式)

**核心任务:**

- 通用的 OLTP 算法优化

  **关键描述:** 

  - 所有 OLTP 算法 API 支持顶点和边的属性**过滤** (减少不必要的图搜索 + 返回数据量大小)
  - 所有 OLTP 算法 API 支持顶点和边**完整信息**跳过/返回 (当前版本仅返回点边id, 或部分属性)
  - 所有接口返回中增加 statistic 统计信息（例如: 遍历的顶点、边数量和**耗时**）

- `kout` 算法查询增加 `DFS` 模式 (原本仅有 `BFS` 模式)

- (**进阶**) 算法实现过程中，支持批量 + 并发(多线程)执行

**技能要求:**

1. 熟练在 Java + Linux 下开发, 理解当前 HugeGraph 的基本图(点边)**存储结构**和设计实现 + **读写**流程
2. 熟悉当前 HugeGraph 的 OLTP 算法的基本设计和**典型实现**原理,
3. 需要有较强的**源码阅读** + 问题分析能力 (任务已有**大量**源码实现可参考/复用)

**mentor**: pzy/待定

**Difficulty:**  middle (2.5星⭐)

**Size**: middle (2星 ⭐, 已有 **9成**源码实现)

**Bonus:** 3k/8k (完成基础/进阶任务)

## 5. 图 ConditionQuery 条件/索引查询优化

**背景:**

数据库中基本的概念是, 主键之外, **属性**的查询要么需要进行全表扫描, 要么需要建立对应的 **(二级)索引**; 在 HugeGraph 中, 图的 `unique` 索引可用于数据查询移除 `NoIndexException`，如未完全命中索引，则可考虑使用某个索引+ConditionQuery.test的方式过滤， 如完全没有索引，则进行全表扫描+ConditionQuery.test方式过滤三种类型A、B、C， A、B建立了基于属性x的索引，当执行 `g.V().has(x, ?)`时，需要查询到A、B索引结果和C的非索引结果

**核心任务:**

1. 对部分命中(未完全命中)的查询进行优化, 避免抛出异常

   **关键描述:** 一个图查询如果部分命中索引, 应该基于命中索引后的部分再做"扫表"查询, 而不应直接抛出异常 or 直接扫全表

**技能要求:**

1. 熟练在 Java + Linux 下开发, 理解当前 HugeGraph 的基本图(点边)**存储结构**和设计实现 + **读写**流程
2. 熟悉当前 HugeGraph 图**索引存储**的细节以及**利弊**点, 能结合需求思考图设计的考量点
3. 需要有较强的**源码阅读** + 问题分析能力 (任务已有**大量**源码实现可参考/复用)

**mentor**: wcb

**Difficulty:**  hard (4星⭐)

**Size**: middle (2星 ⭐, 已有 **9成**源码实现)

**Bonus:** 3k

## 6. 图 Gremlin 的查询并行优化

**背景:**

HugeGraph 现在主要的查询语言 Gremlin 源自图查询语言框架 [Tinkerpop](), 它的 OLTP 查询基本都是使用的"**单线程 + DFS**"的查询方式, 自然在复杂的查询中就会显著遇到瓶颈, 业内有阿里的 [GAIA: A System for Interactive Analysis on Distributed Graphs Using a High-Level Language](https://www.usenix.org/system/files/nsdi21-qian.pdf) 论文对这个问题进行了深入的探讨和重构, 我们需要**部分**实现图 Gremlin 查询的并行化

**核心任务:**

1. 了解基本 Gremlin 查询性能分析方式 + 支持部分语句(`step` 算子)下的 Gremlin 并行化

   **关键描述:** 

   - `HugeGraphStep` 中点/边查找支持批量+并发查询
   - `HugeVertexStep` 中临接点/边的查询支持批量+并发查询

2. 在 Tinkerpop 社区/issue 中寻找/探索进一步的规划和可能方案尝试

3. 包括不限于复用 GAIA-X 中的部分优秀设计/更好的设计方案, 对现有的单 step 并行进行更深的改造 (可选, **进阶项**)

**技能要求:**

1. 熟练在 Java + Linux 下开发, 理解当前 HugeGraph 的基本图(点边)**存储结构**和设计实现 + **读写**流程
2. 了解任何一门图查询语言, 熟悉 `Tinkerpop Gremlin`则更佳 (有教程可学习入门)
3. 有基本的 Paper 阅读能力 + 良好的调研和沟通能力
4. 需要有较强的**源码阅读** + 问题分析能力 (任务已有**大量**源码实现可参考/复用, 亦有基本的设计文档)

**mentor**: suzhaojun

**Difficulty:** middle (3星⭐)

**Size**: low (1星 ⭐, 已有 **9成**源码实现)

**Bonus:** 3k (进阶项完成上升至 12k 起)

**注:** 进阶项任务难度上升至 5 星⭐, 工作量上升至 4 星⭐, 建议感兴趣的同学先阅读一下论文/代码看看理解情况.

## 7. 图的监控和性能观测优化

**背景:**
图引擎在线化，对于稳定性（SLA）要求高，需要完善的监控以及性能监测能力：
* 在线问题排查：能够及时检测和解决图数据库或图处理系统中的线上问题，包括进程故障、异常行为等。
* 统计分析：通过采集和分析关键指标，提供对系统在故障前、故障中和故障后的性能表现的深入理解，以便进行问题诊断和优化。
* SLA保证：作为确保线上服务质量的重要手段，提供对图处理系统的监控和性能观测，以满足和监控系统的服务级别协议（SLA）要求。

**核心任务:**

- 增加 `arthas server` 内嵌服务接口 (也就是说启动 HG Server 后可以直接使用 Arthas 动态观测/perf/debug, 无需单独安装)
- 按照图名称、接口名称计算请求总数、成功数、失败数、平均响应时间、最大响应时间 (接口返回格式兼容 `Prometheus` 即可)
- 增加白名单监控信息
- Slow query 慢查询/慢日志的实现, 类似传统 DB 帮助用户能及时发现查询中的慢语句并方便分析溯源 (**进阶**项, 暂无参考)
- 为 computer 增加 mertics 端点并返回 JVM mertics/superstep Stat/compute 耗时/input 耗时/output 耗时 等 (**进阶**项)

**mentor**: liu / [JackyYangPassion](https://github.com/JackyYangPassion)

**Difficulty:** low (1.5 星⭐)

**Size**: medium (3星 ⭐, 已有 **8成**源码实现)

**Bonus:** 3k/6k (完成基础/进阶)

## 8. 存储引擎 RocksDB 改进与优化

**背景:**

RocksDB 作为 HugeGraph 未来主要的单机/分布式后端存储底座(存储引擎), 因为设计和定位原因(对应 `InnoDB/Myisam`), 所以它自身并不会提供类似完整 Database 那样整套的组件, 甚至也没有命令行工具, 只有裸的 API 调用, 但这样对上层图的调用/性能分析/问题定位等带来了很大的影响, 用户也很难 cover 它, 另外的一个问题是由于之前 `rocksdb-jni`设计上的历史问题, 导致存在大量的胶水配置代码, 使得 HG 修改/新增/调整 RocksDB 的参数, 既不**动态**化, 也很不灵活, 有许多 `hard-code`, 综上原因, 我们很需要对原生 `RocksDB` 进行优化, 可供用户选择 `RocksDB Plus`的选择, 从而在"性能 + 功能 + 易用性"各方面实现提升

任务描述:

- 根据已有资料和文档, 梳理 `RocksDB` 存在的局限性和不足
- 根据代码/文档/slide, 分析 `RocksDB Plus` 的数据结构优化和改进点
- HugeGraph 适配 `RocksDB Plus`, 类似提供一个进阶选择 (同时保持对 `RocksDB` 兼容)
- 编写基本的 perf 代码, 能进行简单性能测试和对比 (可借助 `loader/jmeter` 等压测工具)
- 帮助 HugeGraph 的 CI 中加入 `perf `部分, 这样每次 CI 运行都能有一个 perf 情况对比分析, 观测**读写性能**影响, 可参考业内做法 (**加分项**)

**技能要求:**

1. 熟悉 HugeGraph 的**编译/运行**流程, 熟悉 rocksdb 后端的**初始化/启动**完整过程
2. 熟悉 `Java/Linux` 编程, 了解/可阅读 `C++/Makefile/JNI`更佳
3. 了解 `BTree/LSM/SkipList/Trie Tree` 至少 1~2 种数据结构设计, 了解文件存储/编码压缩更佳
4. 了解 ` RocksDB/HBase/TiKV` 等存储设计(包括 `memtable/wal/compaction`/LSM读写流程), 熟悉源码更佳
5. 需要有较强的问题分析解决能力,  了解性能分析/定位方式/火焰图更佳

**mentor**: [imbajin](https://github.com/imbajin) + lp

**Difficulty:** middle (3.5星 ⭐)

**Size**: middle (3星 ⭐, 需调试和适配, 已有 6 成代码参考)

**Bonus:** 6k (已发布至 GLCC)

**注:** 此任务也是 OSPP [并发集合](https://summer-ospp.ac.cn/org/prodetail/23ec80364?list=org&navpage=org)优化的同类, 与并发集合中的加分项直接关联 (但并不绑定)

## 9. 图功能优化 & 支持子图功能

*补充 ing*

**任务描述:**

- 子图(subgraph) 简单可以理解为全图的任一部分, 这里我们需要按照"**顶点 + 边label + 属性**"过滤生成一张子图, 增加子图 Job
- auth Server删除system graph删除更新权限接口增加租户管理员
- 增加schema模板，创建图空间时，可指定schema模板查询schema groovy格式返回属性改名

**mentor**: zgx

**Difficulty:** medium (3 星⭐)

**Size**: medium (3星 ⭐, 已有 **8成**源码实现)

**Bonus:** 3 ~ 8k (完成部分/全部)

## 10. 图的容器化增强和完善 (SaaS前置任务)

**任务描述:**

核心是参考已有的 [docker-issue](https://github.com/apache/incubator-hugegraph/issues/840), 完成 TODO ✅ 项待完成部分, 更好的让用户和开发者能使用 HugeGraph, 以及为后续的云(SaaS)化做铺垫

-  use [docker-slim](https://github.com/docker-slim/docker-slim) to slim the image
-  keep the tags same with server (like `hugegraph/hugegraph:1.0.0`)
-  use a script to run server & hubble together OR use docker-compose to manage them
-  use docker-compose to manage computer, it needs to contain `etcd` and `hugegraph-server`
-  pre-load some data or graphs in container so that users can traverse the graph with one step (docker run)
-  use DockerHub(autobuild), currently it's not free to use (Submit a OSS request)
- support Cassandra(Docker) as backend (compose with HugeGraph)

**技能要求:**

1. 熟悉 HugeGraph 的**编译/运行**流程, 熟悉基本的初始化/启动过程
2. 会使用/阅读 `Shell`, 需要熟悉 `Docker` 使用, 能编写 `Dockerfile` 更佳
3. 了解 Docker-Compose/K8s 更佳 (加分项)
4. 需要有较强的问题分析解决能力 (容器相关问题需要定位)

**mentor**: [coderzc](https://github.com/coderzc)/[imbajin](https://github.com/imbajin)

**Difficulty:** low (1.5星⭐)

**Size**: middle (3星 ⭐, 需要调试和测试 Docker)

**Bonus:** 2~5k (视 TODO 项完成数)

**备注:** 如果熟悉 k8s, saas 化的同学, 可以完成后承接下一个 `K8s/SaaS` 化的任务 (会独立拆分出来, bonus 单独为 **12k+**)


## 11. 重构官网

任务描述:

- 使用 [Docusaurus](https://docusaurus.io/) 框架来重构官网， 具体请参考已有的任务 [Refactor the official website](https://github.com/apache/incubator-hugegraph-doc/issues/103)
- 优化官网排版与布局
- 优化 hubble 并增加一些 feature, 请参见 https://github.com/apache/incubator-hugegraph-toolchain/issues/382

**mentor**: [coderzc](https://github.com/coderzc)

**Difficulty:** middle (2.5 星⭐)

**Size**: medium (3星 ⭐, 需要适配和调试)

**Bonus:** 6k

## 12. JanusGraph 的新增功能合入 HugeGraph

**背景:**
[JanusGraph](https://github.com/JanusGraph/janusgraph)(前身叫`Titan` 算是分布式图存储的第一代奠基), 社区以海外用户为主, 但是整体结构上其实殊途同归, 也同为 Java 语言开发, 和 HugeGrpah 的关系类似 "LevelDB" VS "RocksDB", 所以我们希望推动两个社区的更多的复用和合作, 首先就可以从 JG 的功能 --> HG 开始

任务描述:

- 根据已有资料和文档, 梳理最新版 `JanusGraph` 代码新增的功能特性 (比较重要的)
- 梳理出核心特性上的区别, 整理为文档, 参与 `HG X JG` 社区联合沟通
- 移植核心的 `feature/bug-fix` 等到 HG 中来, 能改进则更佳
- 有良好的英语表达能力, 能在有稿子 + Slide 的前提下, 进行简单的分享与 QA (**进阶**)

**mentor**: ? + [javeme](https://github.com/javeme)/[imbajin](https://github.com/imbajin)

**Difficulty:** middle (2.5星 ⭐)

**Size**: middle (3.5星 ⭐, 需要阅读两边的源码, 调试和适配)

**Bonus:** 3~12k (视完成度/合并数)

**注:** 同时熟悉两个社区结构和设计的同学是最佳, 这部分也是基于已有代码进行适配和优化

## 13. HugeGraph 的序列化优化/性能优化

*补充 ing*, 先列一下关键点

1. 支持高频属性值的常量枚举编码
2. 增加按需延迟反序列化类BackendProps包裹原始字节
3. 序列化将各bytes替换为bytebuffer减少碎片
4. 堆外内存优化gremlin dedup/emit等等step
5. 邻接边的缓存结构更加精细化，比如以顶点Id为key，增加命中率
6. 优化带目标点id条件的邻接边查询
7. 优化根据顶点查边时，如果顶点类型无该类型边则直接返回空

**mentor**: [zyxxoo](https://github.com/zyxxoo)/[javeme](https://github.com/javeme)/[coderzc](https://github.com/coderzc)

**Difficulty:** middle (2~3.5星 ⭐)

**Size**: middle (1~3.5星 ⭐, 根据 task 不同)

**Bonus:** 2~12k (视完成度/合并数)

### x. 图 Gremlin 的版本升级和新适配

*补充 ing*

**背景:**

目前 HugeGraph 使用的是 `3.5.1` 的 TinkerPop, 最新稳定版本是 `3.6.x`, 修复了大量 `Gremlin`查询的 Bug, 优化了不少 perf/api 相关的优化, 所以 HugeGraph 需要能适配升级到新的 `Gremlin` 版本, 并梳理出主要的变化和提升对比

### x. 分布式锁与柔性事务的初步实现

*补充 ing* 

## FAQ (其他)

1. 已有的**代码 + 文档**参考在哪? 什么时候可以参与/报名/开始任务, 可以**提前开始**参与提交代码么?

   **A:** 参考代码也在 GitHub (原 HugeGraph Org 下), 初步重构 + 设置好权限后, 在 6.1 前后就会公开给可参与的同学, 然后不同于 OSPP 的任务有开始时间, 新增任务可以**随时开始**, 完成即可**提前结束**, 确认完成后即可**结项**领取对应的开源礼品 + 奖金等

2. 新增的任务是否也需要**等截止期**才能确定是否选中? 可否提前联系 mentor, 待定的 task 联系谁?

   **A:** 不是, 不同于 OSPP, 新增的任务可以被**提前预订**选择, 提交了 proposal 之后, 导师和同学都很认可, 则确定无误即可提前占坑(**6.10之后**), 已确定任务的 task 会更新为 "**已预订**", 方便同学及时了解状态

3. 有些 mentor 还是待定或者没有具体的联系方式, 如何沟通?

   **A:** 这部分因为部分 mentor 人选可能还有调整, 所有不确定 mentor 或联系方式的任务可先联系 [imbajin](https://github.com/imbajin), 或公众号加**学生群**, 之后会及时更新同步信息

4. 已经毕业了, 这部分新增任务可以参与么?

   **A:**  `OSPP/CCF` 限定在校学生, 而属于 **HugeGraph 基金会**渠道的方式都可参与, 不受平台限制