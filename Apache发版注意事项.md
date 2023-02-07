> 这篇文档基于官方的[发版指南](https://infra.apache.org/release-distribution.html)做提取和精简, 关注我们在发版过程中最容易忽视/犯错的部分, 所有**参与发版**的同学, 尤其是每个仓库/模块的负责人都需要完整, 仔细的通读一遍, 有不确定的地方务必及时沟通询问
>
> **注:** 下文主要以已加入 incubator 项目为背景**通俗描述**, 不针对毕业项目和专有名词做额外解释说明

## 名词

一些文中出现的常见缩写:

- **ASF**: Apache Software Foundation
- **ASL2.0**: Apache Software License 2.0

新同学可能会比较困惑为何不直接使用 `Apache 项目` 这样的描述而建议/习惯使用 `ASF 项目`, 那是因为 `Apache 项目` 容易产生歧义
使用 Apache 许可协议的**任何项目**都可这样引用, 但是 ASF (基金会) 的项目则有一系列单独的**要求/限定**的, 加以区分可避免大家误解对应的含义, 
也就能更好理解引入 `Apache 项目`(非 ASF) 和引入 `ASF 项目` 依赖的区别了


## LICENSE

这里容易产生许多小问题, 请务必逐条确认检查: (不确定的一律以[官方说明](https://infra.apache.org/licensing-howto.html)为准)

1. 每个源码/二进制包(包括发行的 jar 包)**都**都必须提供 `LICENSE + NOTICE + DISCLAIMER`  文件
   - 源码(source)包必须位于项目**根目录**下
   - 二进制包呢? (参考其他项目一般也在根下, 但不清楚 ASF 是否有硬性要求)
2. `LICENSE` 文件原始版本必须**格式/内容**完整正确, 请直接下载官方提供[版本](https://www.apache.org/licenses/LICENSE-2.0.txt)然后放在项目中
3. `LICENSE/NOTICE` 文件不可包含**不必要**的信息, 如果移除/更新了依赖则必须及时更新/移除对应的 `LICENSE/NOTICE` 信息
4. 引用的第三方/其他 license, 必须将详细信息附加到我们的 `LICENSE` 文件后, 如果引用的 `LICENSE` 很长, 则需要单独存储并指向它们
5. **二进制包**还需要特别注意, 通常它携带的 `LICENSE+NOTICE` 文件内容和源码包有**巨大**区别, 请勿直接**复用**同一文件
   - 源码包通常不携带二进制/jar 包等依赖, 所以它的 LICESEN 和 NOTICE 会干净简单许多, 但它通常需要对**源码**引用做声明
   - 二进制包主要是要对大量引入的依赖做声明, 那它是否需要保持源码中引用的第三方代码声明呢? (这个)
6. (**待定**) 一个依赖如果允许多个 `LICENSE`, (例如 apache & gpl), 建议引入 `LICENSE` 的依赖选择一个 `LICENSE` , 而不是列出所有 (不方便他人 review?)
   - 同理, 如果这个依赖的 LICENSE 文件是独立存在的, 也应该只选取其中所选的内容 (例如去掉 GPL 的声明引用, 待确定? 是这个意思么?)
   - 有些 ASF 项目在 `LICENSE` 文件中引入依赖是列出了所有可选 LICENSE 条目, 但应该不是一个很建议的写法 (应避免参考)



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
2. 同时要注意, 如果是引用了第三方的代码 + License 头, 请**不要**删除/修改对方的 `Copyright` 声明, 更不要通过插件添加了额外的 `ASL2.0`
   - 小修改/增加(对第三方代码), 一般应使用原本一样的 license, 不修改原本的 license/author 内容
   - 大修改, ASF 建议是 PMC 具体讨论处理 (这里没有严格定义"大/小"修改的区分方式, 所以如无必要就视作小修改处理吧)
   - 如果是在一个大(上千行)的文件中引用了一个内部结构体/类 (几十行), 我们此时该如何保留它的 license 头引用呢 (详见文尾单独讨论)
3. 如果引用了其他开源项目的代码, 请不要修改原始的 License Header, 哪怕它格式/语法等有问题, 或者不够完整(精简版)
4. 如果一个软件/代码包含**多个**可选许可, 请考虑以下二选一:
   - 优先选择 Apache 最适配的A 类宽松许可, 避免不必要麻烦
   - 如果原 license header 中已经同时提到了多种可选许可, 则**不用修改**



部分文件**不需要**添加 `License Header`, 原则是基于"内容/结构上没有任何创造性", 如果不能确定, 则需要添加, 以下是参考各大社区项目和官方说明的几个典型**不需要**添加的例子:

1. 简短的文本信息 (典型 `READEME`, `CONTRIBUTING`, `*.txt`, `*.md`, `*.log` 以及各种 lint 配置文件)
2. 增加了头注释可能会报错的文件 (典型 `json`, `svg`文件)
3. 源码打包时**可排除**的文件, 例如 `.github`下的专用 action 文件, `.git` 或类似的

以下是比较典型的**建议**但非强制添加的例子, 简而言之, ASF 建议除开不需要/很难添加的例子外, 应考虑一律加上 `license header` 以减少麻烦

1. 包管理/依赖配置文件, 例如 `Makefile/pom.xml` 等, ASF 建议如无必要, 都加一下免得引入纠纷 (参考邮件[讨论](https://lists.apache.org/thread/l6w0ytfodywfsb6ky0gd41qfzb148r50))
2. 程序生成的模板/用户使用的 `*.conf, *.properties` 文件可视具体情况 PMC 讨论, 不确定的或单元测试中使用的配置文件建议建议默认都带上 (或向上咨询)

另外如果存在压缩的 `css/js` 等文件, 如果是自己项目开发产生的, 则建议使用**较短**版本的声明, 而不建议使用原始的 header 版本

## NOTICE

License 存放自己 + 第三方的许可证比较容易理解, `NOTICE` 文件又是做啥的呢? 简单说它可以存放 Copyright + (法律)强制性许可要求

1. NOTICE 文件必须遵循 ASF 的标准规范, 不可随意修改格式 (建议参考**已发版**过的 `incubating` 项目, 毕业项目可能有历史原因请勿直接照搬)
2. NOTICE 文件的 `Copyright` 年份需要严格统一(例如有多个repo), 并且**最终年份**应该随发版时更新
3. 如果我们引用了其他 ASF 的项目, [参考](https://infra.apache.org/licensing-howto.html#bundle-asf-product) (注意这和引用了 `Apache2.0` 协议的项目不是等同的)
4. 尽可能保持 `NOTICE` 简洁, 不确定的引用请咨询社群/导师, 别先假定需要, 因为它会给使用方(下游)带来负担 (传递性)
5. BSD/MIT 许可证内嵌的 Copyright 通知**不需要**重新引用 ([LEGAL-59](https://issues.apache.org/jira/browse/LEGAL-59))
6. 如果第三方依赖的 `NOTICE` 文件错误的引用了 `LICENSE` 或者其他信息, 我们该如何选择?
   - 照搬原本的错误写法?
   - 移除掉不正确的部分, 只保留合规的声明? (然后再予以说明?)

官方 + incubator示例:

- [最简模板](https://www.apache.org/licenses/NOTICE-2.0.txt) (推荐)
- [seatunnel](https://github.com/apache/incubator-seatunnel/blob/dev/NOTICE) (推荐)
- [HTTP Server](https://www.apache.org/licenses/example-NOTICE.txt) (不太推荐)

## Disclaimer

**孵化中**的项目的任何发行包(包括网站)都需要携带 `DISCLAIMER` (免责声明)文件, 这个听起来很法务化的文件有两个选择: (详见官方[说明](https://incubator.apache.org/policy/incubation.html#disclaimers))

1. **标准版:** 可以遵循 ASF 的所有发布政策的孵化项目, 命名为 `DISCLAIMER` 文件
2. **WIP 版本** (Work In Progress): 意味着发版过程中会**有部分**不能满足 ASF 要求的发布政策, 命名为 `DISCLAIMER-WIP`

两个说明的模板内容是不一样的, WIP 版本需要具体列出"**已知问题列表**", 也就是提醒使用方这些地方可能需要留意检查, 另外要注意的是, 孵化项目在毕业前需要转为标准的 DISCLAIMER 声明 (也就意味着相关发布问题都被解决了)

但是哪些情况是 `WIP` 版本的免责声明可以容忍/携带的呢? 例如说 `GPL` 或是` CC-By` 协议的二进制依赖可以么? 还是说它只适用于一些比较模糊的许可或者是合规性问题呢? (这个需要确认一下)

## Copyright

版权的许可, ASF 的项目要求都放在 `NOTICE` 中而不能是 header 中, 这个是 ASF 单独要求的, 和其他自由使用并添加 Copyright 的项目无关, 并不是 `Apache License` 原本的要求, 特别说明一下以免误会

## GPL

以 GPL 许可为代表的**代码/二进制**引用基本都不能被包含在 Apache 项目中, 或者简单说, 严格限制分发/商业化/的许可证基本都不能被引入, 详细列表参考[官方禁止引用列表](https://www.apache.org/legal/resolved.html#category-x)

这里的不能被包含就不止是说源码中不包含, 编译产生的二进制包理论上也不能包含, 所以使用了类似依赖/插件的部分代码需要移除/重构, 否则会非常棘手, 那有以下可供参考的常见做法:

1. 将这种 Apache 不允许携带的引用变为**可选项**, 例如 oracle 的 `ojdbc.jar` 包, 你可以写文档告诉需要的用户去自行下载然后关联/启用上
1. 如果一个项目协议多种许可里只是包含了 GPL/LGPL, 那是不会影响你使用的
1. 另外要注意 `CC (Creative Commons)` license, 如果单独出现 Apache 也是[不允许](https://www.apache.org/legal/resolved.html#cc-by)的 (这个可能大家很容易忽视, 这里强烈建议使用插件扫描)

## Binary/Archive

二进制文件 +  单独的 jar 包(视作 Archive 文件) 需要特别关注, 可规避大量不必要麻烦:

1. 二进制文件在**源码**包中尽量不要出现, 如果 已存在的考虑通过**编译/测试时**通过**下载** or **临时生成**代替 (新 PR 应避免重新引入)
2. tar 包解压出的普通文件 (例如 `swagger-ui` 可提供 Apache 许可的前端包), 也尽量不要直接引入到源码, 而应在编译时下载代替
3. 部分图片可能也会被视为二进制文件, 这部分如非源码必要, 可考虑打包时排除
4. 如果是必要的图片/二进制, 则需要有清晰的引用说明 (这部分的示例需要找一下)

## Git/GitHub 相关

这里主要是一些容易误解的细节点, 但是出错也会导致发版重回:
1. 发版分支可以进行单独更新, 但是一旦发版 VOTE 邮件发出, 则必须固化下来/停止后序任何更新提交 (否则会被视为不合规)
2. 发版的时候, 因为很可能有**多轮**, 所以建议 tag 使用 rc 后缀, 例如 `1.0.0-rc1` 代表第一次投票, 打回则递增 rc 数字 (非强制但建议)
3. 分支 (branch) 按 ASF [邮件](https://lists.apache.org/thread/k08vq5r4nfos2ptn69w2fbm2mvmkb91n)中提到并不需要, 所以保留 `release-1.0.0` 复用即可 
4. 可以在发版邮件里携带 tag 最近一次的 commit-ID, 方便确认


## Hard Question

这里是一些暂时悬而未定的问题, 大多并没有从  `ASF` 官网上找到足够清晰的定义, 需要和导师/社区一起讨论:

1. 如果引用第三方的代码(源码)后, 需要同时在 **source & binary release** 的不同 `LICENSE` 文件中都添加引用么

   A: 是的, 都需要 (refer [issue](https://github.com/apache/incubator-hugegraph-computer/pull/227#issuecomment-1396795029))

2. 如果引入的第三方代码的 `NOTICE` 原始文件就存在错误/不正确引用, 我们是否需要照搬

   A: 看情况应不用照搬错误部分, 只需要选取需要/合规的部分即可 (refer [issue](https://github.com/apache/incubator-hugegraph-computer/pull/227#discussion_r1081107569))

3. 如果引入的第三方代码 `license header` 中存在 `Copyright xx`, 我们是否应该保留, 还是应该去掉然后在 `NOTICE` 中体现

   - 我们应尽保留原有 `license header` (不去修改它的格式/内容)
   - 只有捐给 ASF 的项目才会触发 `Copyright` 转移到 `NOTICE` 的设定 (*Copyright notices are only relocated if they are donated to the ASF as part of a software grant.*)

4. 如果引入的第三方代码只是某个代码文件中的一小部分, 是否应该用它的 license header 作为整个文件的头 (**待定**)

   - 如果引入的是一个接口定义/子类/结构体, 可否直接在这部分代码片段上引入它的 `license header`
   - 如果不能在代码中间位置引入 `license header`, 那么头部是否允许保留两个`license header` (一个是 ASF 的, 一个是引入的)
   - 如果建议如无必要, 仅保留一个`license header`, 是否需要在 `LICENSE` 文件中说明引入的代码行范围, 不引入的话似乎有点模糊(其他人不知道哪部分代码是 refer 的)
   - 比较建议的还是避免在大文件中引入一小段第三方代码, 可以的话优先考虑分离开 or 自己重写

_持续更新 ing_

---

参考资料:
1. https://incubator.apache.org/guides/releasemanagement.html (incubator 项目发版指南①)
2. https://www.apache.org/foundation/preFAQ.html (常见 ASL2.0 协议使用问题)
3. https://www.apache.org/legal/src-headers.html (常见 License 头引用问题, 包括第三方引用)
4. https://infra.apache.org/licensing-howto.html (如何编写你的 `LICENSE/NOTICE` 文件)
5. ...
 