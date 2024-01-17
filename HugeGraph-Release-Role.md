> 本文档是 Apache HugeGraph 首次发版的记录，部分内容可能已经过时

现在我们专注于发布 Apache 社区的第一个版本 (V1.0), 然后希望社区的小伙伴能一同参与进来携手共进

现在主要是说清楚 [HugeGraph 发版流程](https://github.com/apache/incubator-hugegraph/wiki/HugeGraph-Release-V1.2), 以及参与进来的不同角色的同学能做的事, 分工一下 (发版进度管理见 [HugeGraph Release V1.0.0 TODO list](https://github.com/orgs/apache/projects/115))

当前处于投票阶段, 详情见[投票](https://github.com/apache/incubator-hugegraph/wiki/Release-Voting)相关适宜, 另外请务必先阅读 [License](https://github.com/apache/incubator-hugegraph/wiki/Apache-%E5%8F%91%E7%89%88%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9) 检查工作, 这是首次发版最重要的地方

## 0x00. 社区 Contributor

首先, 如果是一位普通 contributor, 此时您没有仓库的管理权限以及 Apache ID, 所以涉及需要新版本**编译/打包/推送**等事项, 可以先略过跳过, 主要可以参与的事项例如以下:

1. 协助检查多个模块的 license 合规性/完整性 (license 需要几项检查)

   - 文件头部 License: 可使用 `maven-rat` 命令, 检查确保每个源码文件头有一致 & 完整的 `Apache License` (当然如果发现遗漏文件没被插件扫描到可及时反馈), 如果发现有任何文件缺失, 可及时**提交 PR** 补全
   - 每个仓库都应有 Apache 官方要求的 `LICENSE` 和 `NOTICE` 文件, 其中包含对应的内容
   - 通过 RAT 插件如果扫到二进制文件, 请单独记录一下 (issue or PR comment), 然后对照 apache 要求后续补全信息
   - 项目引用的第三方库 (需要插件 + 人工检查 + 记录)
     1. 属于 Apache 2.0 协议的
     2. 不属于 Apache 2.0 协议 (尤其是类 GPL 协议)

2. 提交 PR 修改项目包名, 所有都需改为 `org.apache.xxx`, 目前 commons 仓库已经修改完成 (可参考对应的 [PR](https://github.com/apache/incubator-hugegraph-commons/pull/104)), 最好是一个人负责一个大仓库的改名, 模块很多比较复杂的可以按模块分**多个** PR 提交即可, 目前还有 3 个仓库需要: [toolchain](https://github.com/apache/incubator-hugegraph-toolchain), [server](https://github.com/apache/incubator-hugegraph), [computer](https://github.com/apache/incubator-hugegraph-computer)

   **特别注意:** 由于是**第一次**修改包名, 会导致原有未合入的 PR 代码和改名后产生不一致, 所以需等现有 PR 合入后再开始 (以免产生大量二次修改)

3. 修复安全问题 + 旧代码的简单优化 (Clean Code)

   - 扫出的每个项目的明显 `CVE-XX` 安全隐患, 可以尝试根据提示提交 PR 修复 (可参考每个仓库的 [code-scanning](https://github.com/apache/incubator-hugegraph-toolchain/security/code-scanning) + Maven 安全检查插件)
   - 旧代码中存在拼写错误, 多少空格/缺少括号等问题
   - 旧代码中可以通过 IDEA 明显优化的地方 (可参考 [PR](https://github.com/apache/incubator-hugegraph-commons/pull/110/files))
   - 修正包名更换 (因为 `com.xx` –> `org.apache`) 后没有统一的排版, 比如两个`org.xx`没有合并
   - …..诸如此类的小优化都可以提交 (优先把 `IDEA` 能快速识别的明显问题处理一下, 有些不确定是否改的可以**标记**)

4. 更新发版必然涉及**文档**的优化/调整, 站在使用者/用户角度进行检查

   - 新增功能但是**缺失**了对应的文档
     1. 首先应该在对应 PR 下留言提醒, 看原作者提供文档
     2. 如果原 PR 作者已不再维护, 在 doc 仓库里新建一个 issue 看能否自行补充 (疑问可群里说)
   - 文档过时/错误的内容

5. 发现官网页面 / GitHub 页面 / Wiki 等任何的明显错误, 可以立刻提交相关 PR 修复

   - 例如 404/500 页面或图片不可查看等
   - 老旧 / 未更新的 URL (重点检查包**下载地址**, 另外考虑增加一个方便国内用户更快下载的 CDN 地址)
   - typo/错字等
   - 更新或者优化不必要的部分, 例如添加一些符合 apache 社区或者大家参与的内容 (群二维码/订阅邮箱/slack 等等)

6. 发版进入**投票环节**后, 按校验流程确认每一项正确性, 然后发现任何问题及时反馈; 若正常, 邮件回复 +1 以及检查项

上述任务可自行认领, 然后新建一个 issue/pr, 社区 PMC 会帮忙加入 project 中并予以 assign, 遇到任何不理解的问题可以及时在 issue 或群内**提问**, 然后 PMC 会把常见的 QA 及时更新到 Wiki 这, 也方便以后参与的同学更快上手.

## 0x01. 社区 Committer

可以参与 Contributor 的所有事项, 然后可进一步参与:

- 可申请为某个仓库 (模块)的 owner, 负责管理
  1. 对应模块的编译/打包/推送等工作 (发版流程里除投票外的大部分环节)
  2. 协调参与此模块的 contributor, 在 project 中分配任务
- 及时 review 发版相关 PR, 加快发版效率
- 优化改进社区文档, 将群内的常见 Q&A 更新到 wiki 上
- 引导新的 contributor 参与, 尽可能降低上手门槛等



## 0x02. 社区 PMC

除了前两个角色的工作也可以参与外, 更核心负责整体的规划和安排, 以及模块 owner:

- 编译/打包
- 校验/发起投票
- 最终发版 + Release Note 等
- 任何相关的答疑/协调
- 推选新的 PMC/Committer 等

其中大部分的细节在 [HugeGraph 发版流程](https://github.com/apache/incubator-hugegraph-toolchain/wiki/HugeGraph-Release-V1.2)中单独描述, 这里就不再复述

