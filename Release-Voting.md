## 0x00. 概要

> 发送投票邮件时请使用 `xxx@apache.org` 邮箱发送, 发送前务必在发件箱内设置为**纯文本**格式, 见官方[邮件说明](https://infra.apache.org/contrib-email-tips)
>
> PS: 发邮件前, 建议 double check 确认 (先发给其他 PMC 检查)

然后有以下注意点:

1. 所有指向校验和、签名和公钥的链接都必须引用 Apache [主网站](https://downloads.apache.org), 并检查 [KEYS](https://downloads.apache.org/incubator/hugegraph/KEYS) 文件中有你的 GPG 公钥 (与 release 仓库同步)
  ```bash
  # 注意是直接修改 release svn 中的 KEYS 文件 (not dev)
  svn co https://dist.apache.org/repos/dist/release/incubator/hugegraph/
  # 追加 echo 写入个人 PK >> KEYS 中
  svn ci --username $ASF_USERNAME --password "$ASF_PASSWORD" -m "Append KEYS for Xxx"
  https://downloads.apache.org/incubator/hugegraph/KEYS
  ```
2. 先在 `HugeGraph` 社区投票, 发送邮件至：`dev@hugegraph.apache.org` (可选抄送 mentors, 有导师提前参与验证可加速发版进程)
3. 发送汇总邮件通过后, 再在 `Incubator` 社区投票, 发送邮件至：`general@incubator.apache.org` (毕业后则只需在 HugeGraph 社区投票)
4. 社区投票的邮件正文中的 `${Current Release Manager}`, 填写负责本次发布的人员 (e.g: `Jack Li` )

## 0x01. HugeGraph 投票阶段

1. 发起投票邮件到 `dev@hugegraph.apache.org`。**(P)PMC** 需要先按照**发版文档**校验章节确认发版的正确性，然后再进行投票
2. 经过至少 **72 小时**并统计到 ≥ 3 个 `+1` (P)PMC 票后，才视为正常通过
3. 72h 后可宣布投票结果，发起投票 [RESULT] 邮件到 `dev@hugegraph.apache.org` (模板见下)

#### A.  HugeGraph 社区内投票模板 (dev)

下面的模板仅供参考, 请注意使用纯文本模式发送邮件避免乱码: (特别注意修改**版本号**)

```html
邮件标题：
[VOTE] Release Apache HugeGraph (Incubating) 1.x.x

邮件正文：

Hello HugeGraph Community,

This is a call for vote to release Apache HugeGraph (Incubating) version 1.x.x

Release notes:
https://hugegraph.apache.org/docs/changelog/hugegraph-1.x.x-release-notes/

Source and binary files:
https://dist.apache.org/repos/dist/dev/incubator/hugegraph/1.x.x/

Maven artifacts are available in a staging repository:
https://repository.apache.org/content/repositories/orgapachehugegraph-xxxx (server)
https://repository.apache.org/content/repositories/orgapachehugegraph-xxxx (toolchain)
https://repository.apache.org/content/repositories/orgapachehugegraph-xxxx (computer)
https://repository.apache.org/content/repositories/orgapachehugegraph-xxxx (commons)

Git tag for the release:
https://github.com/apache/incubator-hugegraph/tree/1.x.x
https://github.com/apache/incubator-hugegraph-toolchain/tree/1.x.x
https://github.com/apache/incubator-hugegraph-computer/tree/1.x.x
https://github.com/apache/incubator-hugegraph-commons/tree/1.x.x

Keys to verify the Release Candidate:
https://downloads.apache.org/incubator/hugegraph/KEYS

The GPG user ID(s): xxxx

The vote will be open for at least 72 hours or until the necessary number of votes are reached.

Please vote accordingly:

  [ ] +1 approve
  [ ] +0 no opinion
  [ ] -1 disapprove with the reason

Checklist for reference:

  [ ] Download links are valid.
  [ ] Checksums and PGP signatures are valid.
  [ ] Source code distributions have correct names matching the current release.
  [ ] LICENSE and NOTICE files are correct for each HugeGraph repo.
  [ ] All files have license headers if necessary.
  [ ] No unlicensed compiled archives bundled in source archive.

More detail checklist  please refer:
https://cwiki.apache.org/confluence/display/INCUBATOR/Incubator+Release+Checklist

Steps to validate the release，Please refer to:
https://hugegraph.apache.org/docs/contribution-guidelines/validate-release/ (EN)
https://hugegraph.apache.org/cn/docs/contribution-guidelines/validate-release/ (CN)


Thanks,
${Current Release Manager}
```

Note:
- 这封邮件由本次**发版负责人**发出, 请先**测试发送**查看排版是否正常 (务必)
- 发送邮件后, 请从[hugegraph-dev 邮箱](https://lists.apache.org/list?dev@hugegraph.apache.org)里复制邮件链接发到 committer/发版群
- ASF 推荐发版可使用 apache 邮箱方便身份确认, 请提前在 Gmail 或其他邮箱里配置 apache 邮箱发件设置并**测试**
- 参与投票验证的同学, 验证内容 + 回复邮件模板参考[此处](https://hugegraph.apache.org/cn/docs/contribution-guidelines/validate-release/#%E9%82%AE%E4%BB%B6%E6%A8%A1%E6%9D%BF)

#### B. 关闭投票邮件

如果投票已达到所需票数(≥3)后，进行结果统计前，需要直接回复投票邮件，说明关闭本次投票会话

```html
Hi,

Thanks, everyone, I will close this vote thread and the results will be tallied.

Best wishes!

${Current Release Manager}
```

#### C. 取消投票（可选）

如果反馈了一些**严重问题**，需要修复后重新发布，则需要先取消投票，发版负责人需要发送**取消投票**邮件并进行说明:

```html
邮件标题：
[CANCEL][VOTE] Release Apache HugeGraph (Incubating) ${release_version}

邮件正文：
Hello HugeGraph Community,

I'm cancelling this vote [投票链接] because of license issues. I'll fix them and start
the round 2 vote process.
    
The detail of the modifications are as follows:
    
1. Remove the file xxx
2. Removes the files be built from xxx
    
Thanks a lot for all your help.

${Current Release Manager}
```

#### D. 宣布投票结果

注：该邮件的 thread 地址可通过访问 [hugegraph-dev 邮箱](https://lists.apache.org/list?dev@hugegraph.apache.org)记录页面查到 (加载时间可能会比较长), 然后选择相应邮件点进去后即可生成 thread 链接

```html
邮件标题：
[RESULT][VOTE] Release Apache HugeGraph (Incubating) ${release_version}

邮件正文：
Hello Apache HugeGraph PPMC and Community,

The vote closes now as 72hr have passed. The vote PASSES with

xx (+1 binding) votes from the PPMC (xx, xx, ...)
xx (+1 non-binding) votes from the rest of the developer community (xx, xx)
and no further 0 or -1 votes.

The vote thread: {vote_mail_address}

I will now bring the vote to general@incubator.apache.org to get approval by the IPMC.
If this vote passes also, the release is accepted and will be published.

Thank you for your support.
${Current Release Manager}
```

Note:

- PPMC: Podling Project Management Committee (PPMC) , 也就是当前孵化项目的 PMC
- IPMC:  Incubator Project Management Committee (IPMC) 一般是 incubator 社区的 PMC，例如 HugeGraph 的导师都是这个角色

## 0x02. Incubator 投票阶段

类似的, 如同上面的步骤, 也有几个投票模板, 这里的投票人主要是整个社区的前辈 (包括当前项目的导师) 

1. Incubator 社区投票，发起投票邮件到 `general@incubator.apache.org`
2. 等待至少 72h, 且需 IPMC Member ≥ 3 个 `+1` 投票，才可进入下一阶段
3. 宣布投票结果，发起投票结果邮件到 `general@incubator.apache.org` 并抄送邮件至 `dev@hugegraph.apache.org`

#### A. Incubator 社区投票模板 (general)

参考示例: (之后可以把上一次的**投票链接**放此处参考, 记得修改**版本号**)

```html
邮件标题：[VOTE] Release Apache HugeGraph(Incubating) 1.0.0

邮件正文：

Hello Incubator Community,

This is a call for a vote to release Apache HugeGraph(Incubating) version 1.0.0

The Apache HugeGraph community has voted on and approved a proposal to release
Apache HugeGraph(Incubating) version 1.0.0

We now kindly request the Incubator PMC members review and vote on this
incubator release.

HugeGraph community vote thread:
  • [HugeGraph社区投票链接]

Vote result thread:
  • [HugeGraph社区投票结果链接]

The release candidate:
  • https://dist.apache.org/repos/dist/dev/incubator/hugegraph/1.0.0

Git tag for the release:
  • https://github.com/apache/incubator-hugegraph/tree/1.0.0

Release notes:
  • https://hugegraph.apache.org/xxx/release-notes-${release_version}

The artifacts signed with PGP key [参与发版人的KEY], corresponding to [pgp对应的邮箱],
that can be found in public keys file:
  • https://downloads.apache.org/incubator/hugegraph/KEYS

The vote will be open for at least 72 hours or until necessary number of votes are reached.

Please vote accordingly:
  [ ] +1 approve
  [ ] +0 no opinion
  [ ] -1 disapprove with the reason

More detail checklist  please refer:
• https://cwiki.apache.org/confluence/display/INCUBATOR/Incubator+Release+Checklist

Steps to validate the release，Please refer to:
https://hugegraph.apache.org/docs/contribution-guidelines/validate-release/ (EN)
https://hugegraph.apache.org/cn/docs/contribution-guidelines/validate-release/ (CN)


Thanks,
On behalf of Apache HugeGraph(Incubating) community

```



#### B. 关闭投票

如果投票已达到所需票数后，进行结果统计前，需要直接回复投票邮件，说明关闭本次投票会话

```html
Hi,

Thanks, everyone, I will close this vote thread and the results will be tallied.

Best wishes!

Apache HugeGraph(Incubating) community
```

#### C. 取消投票 (可选)

如果社区导师反馈了一些严重问题，需要修复后重新发布，则需要取消投票，发布负责人需要新起取消投票邮件并进行说明:

```html
邮件标题：
[CANCEL][VOTE] Release Apache HugeGraph (Incubating) ${release_version}

邮件正文：
Hello Incubator Community,

I'm cancelling this vote [投票链接] because of license issues. I'll fix them and start
the round 2 vote process.
    
The detail of the modifications are as follows:
    1. Remove the file xxx
    2. Removes the files be built from xxx
    
Thanks a lot for all your help.

Apache HugeGraph(Incubating) community
```

#### D. 宣布投票结果

参考示例, 内容同时抄送至 `dev@hugegraph.apache.org`, 代表此次发版正式完成

```html
邮件标题：[RESULT][VOTE] Release Apache HugeGraph 1.0.0

邮件正文：
Hi all,

Thanks for reviewing and voting for Apache HugeGraph(Incubating) 1.0.0
release, I am happy to announce the release voting has passed with [投票结果数]
binding votes, no +0 or -1 votes. Binding votes are from IPMC

   - xxx
   - xxx
   - xxx

The voting thread is:
[Incubator社区投票链接]

Many thanks for all our mentors helping us with the release procedure, and
all IPMC helped us to review and vote for Apache HugeGraph(Incubating) release. I will
be working on publishing the artifacts soon.

Thanks
On behalf of Apache HugeGraph(Incubating) community
```

至此**两阶段**投票流程才宣告正式完成 ✅, 发版至此已经完成了 `95%`, 最后的事项见原发版 Wiki 最后章节

## 0x03. 其他

如若因投票邮件内容有小问题（如链接问题):

- 如果发现得比较早，可以取消之前的投票，进行再次投票，如果已经进行比较久，可以由**版本发布人**直接对投票邮件进行回复说明
- 源码物料不做修改，邮件标题可以添加（Round2）区分，如 `[VOTE] Release Apache HugeGraph (Incubating) 1.0.0 (Round2）`
