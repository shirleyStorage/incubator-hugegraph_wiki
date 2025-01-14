> On the way, coming soon~ Contact us if u want to try~

# 1. 图加工: 基于路径的起点与终点构建新边类型 ([EN-Version](https://github.com/apache/incubator-hugegraph/wiki/Graph-Traversal-Expansion))

## 描述

在大数据处理场景中，为了更好地利用图数据库 hugegraph，我们提出了一个新的图加工/扩展功能。
该功能旨在根据指定规则进行路径游走，然后基于该路径的起点和终点，构建一条新边。

举例：在家庭族谱中，以每个顶点为起点，按照路径 `father->person(性别为男)->father` 又走到第三个节点，建立新变，其边类型为 grandfather.


## 需求标准

1. 起点可通过规则圈定。
2. 实现基于指定的路径实现路线游走。
3. 实现该路径的起点到终点构建新边的逻辑。
4. 完成相关单元测试UT。
5. 完成用户使用文档撰写

## 技术要求

- 熟悉图数据库/hugegraph 优先
- 具备 Java/Scala 研发能力
- 熟悉 Linux 基本使用

## 时间节点

1. 上手初步使用了解 `hugegraph`，并可完成本地环境的搭建和调试
2. 设计本功能的实现方案
3. 功能开发和测试用例开发，优化性能
4. 完善用户使用文档

注: 难度普通, 工作量普通