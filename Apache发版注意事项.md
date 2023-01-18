> 这篇文档基于官方的[发版指南](https://infra.apache.org/release-distribution.html)做提取和精简, 关注我们在发版过程中最容易忽视/犯错的部分, 所有**参与发版**的同学, 尤其是每个仓库/模块的负责人都需要完整, 仔细的通读一遍, 有不确定的地方务必及时沟通询问
>
> **注:** 下文主要以已加入 incubator 项目为背景**通俗描述**, 不针对毕业项目和专有名词做额外解释说明

## 名词

一些文中出现的常见缩写:

- **ASF**: Apache Software Foundation
- **APL2.0**: Apache License 2.0

## LICENSE

这里容易产生许多小问题, 请务必逐条确认检查: (不确定的一律以[官方说明](https://infra.apache.org/licensing-howto.html)为准)

1. 每个源码/二进制包(包括发行的 jar 包)**都**都必须提供 `LICENSE + NOTICE`  文件, 其中源码(source)包必须位于项目**根目录**下 (二进制包呢?)
2. `LICENSE` 文件原始版本必须**格式/内容**完整正确, 请直接下载官方提供[版本](https://www.apache.org/licenses/LICENSE-2.0.txt)然后放在项目中
3. `LICENSE/NOTICE` 文件不可包含**不必要**的信息, 如果移除/更新了依赖则必须及时更新/移除对应的 `LICENSE/NOTICE` 信息
4. **二进制包**还需要特别注意, 通常它的内容和源码包的 `LICENSE+NOTICE` 文件有**巨大**区别, 因为源码包通常不携带任何二进制依赖



除了阅读文档外, 最好的办法之一就是参考**官方示例**/其他 incubator 项目, 然后仍不确定的地方及时询问导师/社区 (不要自己猜测)

- [httpd - source](https://svn.apache.org/repos/asf/httpd/httpd/trunk/LICENSE)
- [seatunnel - source](https://github.com/apache/incubator-seatunnel/blob/dev/LICENSE)
- [seatunnel - binary](https://github.com/apache/incubator-seatunnel/blob/dev/seatunnel-dist/release-docs/LICENSE)
- [linkis - source](https://github.com/apache/linkis/blob/master/LICENSE)
- [linkis - binary](https://github.com/apache/linkis/blob/master/linkis-dist/release-docs/LICENSE)

## License Header

首先, Apache 要求每个源文件都有合适的 License, 考虑到简洁性它就规定在**文件头部**引用一个简略版本, 简称 header, 然后完整版本放在根下(`LICESEN`文件)中, 其实是这样的引用关系.



1. 自己项目 License 文件头中**不能**包含 `Copyright` 声明, 这部分应该考虑:
   - 如果不必要, 例如捐赠之前的 Copyright 自愿舍弃, 那直接移除即可
   - 如果需要, 则在 `NOTICE` 文件中声明
2. 同时要注意, 如果是引用了第三方的代码 + License 头, 请**不要**删除/修改对方的 `Copyright` 声明, 更不要通过插件添加了额外的 `APL2.0`
   - 对引用的第三方代码的小修改/增加, 一般应使用原本一样的 license, 不修改原本的 license/author 内容
   - 大修改, ASF 建议是 PMC 具体讨论处理 (这里没有严格定义"大/小"修改的区分方式, 所以如无必要就视作小修改处理吧)
3. 如果引用了其他开源项目的代码, 请不要修改原始的 License Header, 哪怕它格式/语法等有问题, 或者不够完整(精简版)
4. 如果一个软件/代码包含**多个**可选许可, 请考虑以下二选一:
   - 优先选择 Apache 最适配的A 类宽松许可, 避免不必要麻烦
   - 如果原 license header 中已经同时提到了多种可选许可, 则**不用修改**



部分文件**不需要**添加 `License Header`, 原则是基于"内容/结构上没有任何创造性", 如果不能确定, 则需要添加, 以下是参考各大社区项目和官方说明的几个典型不需要添加的例子:

1. 简短的文本信息 (典型 `READEME`, `CONTRIBUTING`, `*.txt`, `*.md`, `*.log` 以及各种 lint 配置文件)
2. 增加了头注释可能会报错的文件 (典型 `json`, `svg`文件)
3. 源码打包时**可排除**的文件, 例如 `.github`下的专用 action 文件, `.git` 或类似的
4. 用户使用的 `*.conf, *.properties` 文件可视具体情况 PMC 讨论, 不确定的 + 单元测试中使用的配置文件建议建议默认都带上 (或向上咨询)

另外如果存在压缩的 `css/js` 等文件, 如果是自己项目开发产生的, 则建议使用**较短**版本的声明, 而不建议使用原始的 header 版本

## NOTICE

License 存放自己 + 第三方的许可证比较容易理解, `NOTICE` 文件又是做啥的呢? 简单说它可以存放 Copyright + (法律)强制性许可要求

1. NOTICE 文件必须遵循 ASF 的标准规范, 不可随意修改格式 (建议参考**已发版**过的 `incubating` 项目, 毕业项目可能有历史原因请勿直接照搬)
2. NOTICE 文件的 `Copyright` 年份需要严格统一(例如有多个repo), 并且**最终年份**应该随发版时更新
3. 如果我们引用了其他 ASF 的项目, [参考](https://infra.apache.org/licensing-howto.html#bundle-asf-product) (注意这和引用了 `Apache2.0` 协议的项目不是等同的)
4. 尽可能保持 `NOTICE` 简洁, 不确定的引用请咨询社群/导师, 别先假定需要, 因为它会给使用方(下游)带来负担 (传递性)
5. BSD/MIT 许可证内嵌的 Copyright 通知**不需要**重新引用 ([LEGAL-59](https://issues.apache.org/jira/browse/LEGAL-59))

官方 + incubator示例:

- [最简模板](https://www.apache.org/licenses/NOTICE-2.0.txt) (推荐)
- [seatunnel](https://github.com/apache/incubator-seatunnel/blob/dev/NOTICE) (推荐)
- [HTTP Server](https://www.apache.org/licenses/example-NOTICE.txt) (不太推荐)



## Copyright

版权的许可, ASF 的项目要求都放在 `NOTICE` 中而不能是 header 中, 这个是 ASF 单独要求的, 和其他自由使用并添加 Copyright 的项目无关, 并不是 `Apache License` 原本的要求, 特别说明一下以免误会



## GPL

以 GPL 许可为代表的**代码/二进制**引用基本都不能被包含在 Apache 项目中, 或者简单说, 严格限制分发/商业化/的许可证基本都不能被引入, 详细列表参考[官方禁止引用列表](https://www.apache.org/legal/resolved.html#category-x)

这里的不能被包含就不止是说源码中不包含, 编译产生的二进制包理论上也不能包含, 所以使用了类似依赖/插件的部分代码需要移除/重构, 否则会非常棘手, 那有以下可供参考的常见做法:

1. 将这种 Apache 不允许携带的引用变为**可选项**, 例如 oracle 的 `ojdbc.jar` 包, 你可以写文档告诉需要的用户去自行下载然后关联/启用上
1. 如果一个项目协议多种许可里只是包含了 GPL/LGPL, 那是不会影响你使用的
1. 另外要注意 `CC (Creative Commons)` license, 如果单独出现 Apache 也是[不允许](https://www.apache.org/legal/resolved.html#cc-by)的 (这个可能大家很容易忽视, 这里强烈建议使用插件扫描)

## Binary/Archive

二进制文件 +  单独的 jar 包(视作 Archive 文件) 需要特别关注, 可规避大量不必要麻烦:

1. 二进制文件在**源码**包中尽量不要出现, 如果存在的考虑通过**编译/测试时**下载代替, 
2. tar 包解压出的普通文件 (例如 `swagger-ui` 可提供 Apache 许可的前端包), 也尽量不要直接引入到源码, 而应在编译时下载代替
3. 