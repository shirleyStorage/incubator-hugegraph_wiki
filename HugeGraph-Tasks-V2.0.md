> For Students & 开源之夏扩展的完整版本, 开源之夏原有议题不受影响, 这边是全新扩展出的更多任务, 随时更新ing
>  **注:** 开始任务前, 请**务必**先使用 + 阅读一下已有的 HugeGraph 相关文档/文章/Slide (参考[资料](https://github.com/apache/incubator-hugegraph-doc/issues/207)), 把主流程和背景大概清楚后, 再开始阅读源码

下列任务基本都位于 [Server](github.com/apache/hugegraph) 主仓库 (github.com/apache/hugegraph), 基本都是核心的**数据结构/存储结构/性能优化/新增功能/分布式存储**这几块, 不同于开源之夏 [OSPP](https://summer-ospp.ac.cn/org/orgdetail/ec8b597b-248f-4592-bd1c-be22e52a5654?lang=zh) 列表已有的任务, 特点如下:
1. 下面新增的所有任务(不包括进阶要求)都是有**大量**的已有源码 + 设计文档可参考的, 大幅降低了参与的门槛 + 难度/工作量
2. 给出了每个任务的评估难度/工作量等, 大家可根据个人情况自由选择(1 ~ N个)
3. 多数任务有基础/进阶两个部分, 完成一部分亦可获得奖金, 更灵活
4. OSPP 中提交了任务/中选的同学, 也可同时参与这部分任务(前提是有余力)
5. 需要注意同样需要提交基本的 `proposal` 和时间规划, 有类似的结项要求

只是参与方式, 任务量安排/门槛上更加灵活, 目前支持下面 2 种, 大家可以根据个人倾向自行选择:

**任务参与方式**:

1. [CCF协会](https://www.gitlink.org.cn/glcc) 开源活动中参与模式 (限制是需要在 CCF 专属的代码平台[类似 gitee]进行提交与审阅等, 同步更新较为麻烦)
2. Apache HugeGraph 基金会直接分配模式, 社区独立的任务模式 (无额外第三方平台)

注: 因与 OSPP 官方沟通后, 平台已经不允许再新增/修改任何 Task, 所以目前先考虑这两种方式, 有更好的方式或者平台提交任务可随时告知

#### 0. 分布式存储的 2.0 大融合

在之前开源之夏的分布式存储任务[基础](https://summer-ospp.ac.cn/org/prodetail/23ec80344?list=org&navpage=org)上, 诚邀熟悉`分布式存储/RocksDB/LSM` 的同学参与其他相关任务, 这里会持续更新拆分 (或是有意报名 OSPP 项目担心落选的同学也可联系)

#### 1. Server 图存储优化与元信息独立化

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

**mentor**: imbajin

**Difficulty:** middle (3 星⭐)

**Size**: middle (3 星⭐)

**Bonus:** 8k/10k (核心/全部完成)

#### 2. 边(Edge)的数据结构增强 (父子边)

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

**mentor**: 待定

**Difficulty:** middle (2.5星⭐)

**Size**: middle (3 星⭐)

**Bonus:** 3k (完成进阶项则奖金**翻倍**)

#### 3/4. 图 OLTP 算法的增强与优化

**背景:**

了解了 HugeGraph 的读/查询流程之后, 可知道我们主要提供两种查询方式 :① Gremlin/Cypher 的通用图语言查询 ② 单独的 `RESTful` 图算法(例最短路径/K 步邻居/相似性等), ①和②各有利弊, 那么这个的任务是对 ② 的大量已有算法进行优化可功能上的扩展, 减少它灵活性上的不足, 优化查询性能, 它的另一个进阶的任务是可使得 OLTP 算法都能并行去执行 (目前大部分是**串行/单线程**模式)

**核心任务:**

- 通用的 OLTP 算法优化

  **关键描述:** 所有接口支持顶点和边的属性过滤所有接口支持顶点和边信息返回所有接口增加统计信息（以及遍历的顶点、边数和**耗时**）

- `kout` 算法查询增加 `DFS` 模式 (原本仅有 `BFS` 模式)

- (**进阶**) 算法实现过程中，支持批量 + 并发(多线程)执行

**技能要求:**

1. 熟练在 Java + Linux 下开发, 理解当前 HugeGraph 的基本图(点边)**存储结构**和设计实现 + **读写**流程
2. 熟悉当前 HugeGraph 的 OLTP 算法的基本设计和**典型实现**原理,
3. 需要有较强的**源码阅读** + 问题分析能力 (任务已有**大量**源码实现可参考/复用)

**mentor**: pzy/待定

**Difficulty:**  middle (2.5星⭐)

**Size**: middle (2星 ⭐)

**Bonus:** 3k/8k (完成基础/进阶任务)

#### 5. 图 `CondtionQuery` 条件/索引查询优化

**背景:**

数据库中基本的概念是, 主键之外, **属性**的查询要么需要进行全表扫描, 要么需要建立对应的**(二级)索引**; 在 HugeGraph 中, 图的 `unique` 索引可用于数据查询移除 `NoIndexException`，如未完全命中索引，则可考虑使用某个索引+ConditionQuery.test的方式过滤， 如完全没有索引，则进行全表扫描+ConditionQuery.test方式过滤三种类型A、B、C， A、B建立了基于属性x的索引，当执行 `g.V().has(x, ?)`时，需要查询到A、B索引结果和C的非索引结果

**核心任务:**

1. 对部分命中(未完全命中)的查询进行优化, 避免抛出异常

   **关键描述:** 一个图查询如果部分名中索引, 应该基于命中索引后的部分再做"扫表"查询, 而不应直接抛出异常 or 直接扫全表

**技能要求:**

1. 熟练在 Java + Linux 下开发, 理解当前 HugeGraph 的基本图(点边)**存储结构**和设计实现 + **读写**流程
2. 熟悉当前 HugeGraph 图**索引存储**的细节以及**利弊**点, 能结合需求思考图设计的考量点
3. 需要有较强的**源码阅读** + 问题分析能力 (任务已有**大量**源码实现可参考/复用)

**mentor**: wcb

**Difficulty:**  hard (4星⭐)

**Size**: middle (2星 ⭐)

**Bonus:** 3k

#### 5. 图 Gremlin 的查询并行优化

**背景:**

HugeGraph 现在主要的查询语言 Gremlin 源自图查询语言框架 [Tinkerpop](), 它的 OLTP 查询基本都是使用的"**单线程 + DFS**"的查询方式, 自然在复杂的查询中就会显著遇到瓶颈, 业内有阿里的 [GAIA: A System for Interactive Analysis on Distributed Graphs Using a High-Level Language](http://imbajin.com/2021-10-14-Gremlin%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E7%B3%BB%E7%BB%9FGAIA%E8%AE%BA%E6%96%87%E4%BA%8C/) 论文对这个问题进行了深入的探讨和重构, 我们需要**部分**实现图 Gremlin 查询的并行化

**核心任务:**

1. 了解基本 Gremlin 查询性能分析方式 + 支持部分语句(`step` 算子)下的 Gremlin 并行化

   **关键描述:** 

   - `HugeGraphStep` 中点/边查找支持批量+并发查询
   - `HugeVertexStep` 中临接点/边的查询支持批量+并发查询

2. 在 Tinkerpop 社区/issue 中寻找/探索进一步的规划和可能方案尝试

3. 包括不限于复用 GAIA-X 中的部分优秀设计/更好的设计方案, 对现有的单 step 并行进行更深的改造 (可选, **加分项**)

**技能要求:**

1. 熟练在 Java + Linux 下开发, 理解当前 HugeGraph 的基本图(点边)**存储结构**和设计实现 + **读写**流程
2. 了解任何一门图查询语言, 熟悉 `Tinkerpop Gremlin`则更佳 (有教程可学习入门)
3. 有基本的 Paper 阅读能力 + 良好的调研和沟通能力
4. 需要有较强的**源码阅读** + 问题分析能力 (任务已有**大量**源码实现可参考/复用, 亦有基本的设计文档)

**mentor**: suzhaojun

**Difficulty:** middle (3星⭐)

**Size**: low (1星 ⭐, 已有 **9成**源码实现)

**Bonus:** 3k (加分项完成上升至 12k 起)

**注:** 进阶项任务难度上升至 5 星⭐, 工作量上升至 4 星⭐, 建议感兴趣的同学先阅读一下论文看看理解情况.

#### 6. 图的监控和性能观测优化

*补充 ing*

任务描述:

- 增加 `arthas server` 内嵌服务接口 (也就是说启动 HG Server 后可以直接使用 Arthas 动态观测/perf/debug, 无需单独安装)
- 按照图名称、接口名称计算请求总数、成功数、失败数、平均响应时间、最大响应时间 (接口返回格式兼容 `Prometheus` 即可)
- 增加白名单监控信息

#### 7. 图功能优化 & 支持子图功能

*补充 ing*

任务描述:

- 按照顶点和边label和属性过滤生成一张子图增加子图Job
- auth Server删除system graph删除更新权限接口增加租户管理员
- 增加schema模板，创建图空间时，可指定schema模板查询schema groovy格式返回属性改名

#### 8. 图的容器化增强和完善 (SaaS前置任务)

核心是参考 [docker-issue](https://github.com/apache/incubator-hugegraph/issues/840), 完成 TODO ✅ 项待完成部分

**mentor**: coderzc/imbajin

**Difficulty:** low (1.5星⭐)

**Size**: middle (3星 ⭐, 需要调试和测试)

**Bonus:** 3~6k (视 TODO 项完成数)

备注: 如果熟悉 k8s, saas 化的同学, 可以完成后承接下一个 K8s/SaaS 化的任务 (独立拆分出来)

#### 9. JanusGraph 的新增功能合入 HugeGraph

**背景:**
[JanusGraph](https://github.com/JanusGraph/janusgraph)(前身叫`Titan` 算是分布式图存储的第一代奠基), 社区以海外用户为主, 但是整体结构上其实殊途同归, 也同为 Java 语言开发, 和 HugeGrpah 的关系类似 "LevelDB" VS "RocksDB", 所以我们希望推动两个社区的更多的复用和合作, 首先就可以从 JG 的功能 --> HG 开始

任务描述:

- 根据已有资料和文档, 梳理最新版 JG 代码新增的功能特性 (比较重要的)
- 移植核心的 feature/bug-fix 等到 HG 中来

**mentor**: javeme/imbajin

**Difficulty:** middle (2.5星 ⭐)

**Size**: middle (3.5星 ⭐, 需要阅读两边的源码, 调试和适配)

**Bonus:** 3~12k (视合并项/完成数)

备注: 同时熟悉两个社区结构和设计的同学是最佳, 这部分也是基于已有代码进行适配和优化 

#### 10. HugeGraph 的序列化优化/性能优化

*补充 ing*, 先列一下关键点

1. 支持高频属性值的常量枚举编码
2. 增加按需延迟反序列化类BackendProps包裹原始字节
3. 序列化将各bytes替换为bytebuffer减少碎片
4. 堆外内存优化gremlin dedup/emit等等step
5. 邻接边的缓存结构更加精细化，比如以顶点Id为key，增加命中率
6. 优化带目标点id条件的邻接边查询
7. 优化根据顶点查边时，如果顶点类型无该类型边则直接返回空

**mentor**: zyxxoo/javeme

#### x. 图 Gremlin 的版本升级和新适配

*补充 ing*

#### x. 分布式锁与柔性事务的初步实现

*补充 ing*