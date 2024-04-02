> 当一个项目进入 Apache 基金会孵化后, 最重要的第一个事就是玩转起社区的流程, 其中发布新版本 (发版) 是其中的重中之重, 单独记录一下

<!-- more -->

## 0x00. 前言

虽然 apache 社区提供了一系列的文档, 但是由于过于繁多, 缺乏简要的提炼, 所以大家可能看一会就会有点晕头转向, 导致参与/执行效率很低, 后面核心参考官方文档 + 已有的开源项目实践, 尽可能以**通俗易懂**的方式把流程理顺, 也供需要的同学参考 (欢迎交流)

#### 1. 简要说明

发版分为 3 个大方面, 一般来说我们需要都覆盖, 但是发版中 apache 要求最先保证的是(第一个)源码包:

- (**必选**) 第一个是 apache 基金会必要的, 以代码包(source release)为核心 
- (可选) 第二个是用户常用/所需的, 二进制包(binary release)为代表
- (可选) 第三个是方便用户使用而发布到**第三方**平台的发版: 例如 `maven` 提供 jar 包, `dockerhub` 提供容器镜像等

对用户来说, 两个**可选项**是更常用/重要的, 所以尽管可选, 但我们也不能略去, 它使得第一次发版的整个流程就比较长一些, 简化起见下面分成**两类**角色:

1. 主打包/发版的同学 (参与全部流程)
2. 其他参与同学 (contributor: 主要参与配置 + 验证流程, 对应下方的 step1/2/4/5)

#### 2. 整体流程

这里我们仅以 maven 类的项目为例, 进行说明(其他语言/包管理的项目请勿照搬), 虽然 apache 基金会的前辈说项目无法编译也可以发版, 但正常情况下倒不用这么仓促, 参考现有的主流项目做法, 需要有这几个主要流程:

1. 前期准备
2. 配置 maven + svn
3. 生成暂存的二进制/源码包并推送
4. 验证包的完整性 + 正确性
5. 邮件投票
6. 正式发布

后面我会以 `apache-hugegraph` 的多个项目模块(server/toolchain/commons)为例, 给大家记录下所需的流程. 下面不少配置都是**初次**配好后就可重复使用的, 所以前面看起来比较多实际并不会太麻烦, 不用担心 :)

PS: 第一次发版由于涉及包名修改和不少配置初始化, 模块顺序是 **"commons –> toolchain –> server"** (目前处于 server 即将完结的阶段), 准备进入投票

## 0x01. 初始 GPG

之前没有用过, 或者不熟悉 GPG 的同学可以参考[此文](https://www.ruanyifeng.com/blog/2013/07/gpg.html)简单了解/配置, 简单说它就是为了验证**发版人/发布者**身份的验证器 (后文假定已经初步了解)

最简单的例子以 github 提交为例, 默认 git 的一次提交只需要 "**用户名 + 邮箱**" 标识就可以, 但是它是可以任意填写/伪造的, 并且一个开发者也可能有不同的环境/邮箱, 如何确保提交到远端 (github/gitbox) 的代码的安全性呢, 通过 GPG 在本地生成一个密钥, 设置后对你的所有提交自动加上**签名**

#### 1. 本地生成 pgp 公私钥

假定你的 Mac/Win 环境已经有了 GPG 的基本环境, 命令行/ GPG Keychain 中生成好本地 pgp 公私钥(绑定好邮箱 + 上传服务器), 跟着 GUI 其实就能很简单的配置好了, 然后它会自动提示你上传到服务器并返回状态等. (命令行就参考下面的)

```bash
# 0. 自行安装 gpg 后,确认版本 > 2.x
gpg --version

# 1. 生成一对新密钥
gpg --full-gen-key # 然后根据提示选择即可 (GUI 下更直观)
# 根据 apache 官方建议, 加密强度选择 RSA-4096 (私钥密码可选, 非必要)

# 1.2 查看你的 keyID + 完整指纹
gpg  --keyid-format SHORT --list-keys # 8位的是 keyID

# 2. 查看 key 的 ID, 然后发送给公开服务器 (参考上文即可. 不再列出)
gpg --keyserver keyserver.ubuntu.com --send-key keyID # GUI 中一键上传

ls ~/.gnupg # 最后确认本地 gpg 文件夹内配置文件完整
```

然后我是没有单独给私钥添加(密码) `passphrase`, 避免后续二次加密麻烦 (看个人喜好), 其他没啥

#### 2. 上传到 Apache 服务器

这里上传到 Apache 有两个地方需要, 一个是个人设置里, 另一个是 apache 托管发版包的 svn 仓库

##### 1. Apache 个人设置

下一个就是与你的 [Apache 账号](https://id.apache.org/)进行绑定 (类似 github 上绑定 PGP), 你需要在设置里配置对应的**指纹**(如下):

![image](https://github.com/apache/incubator-hugegraph/assets/79143929/82e8ac3b-f8f6-4d09-8e6a-d9f29e111f57)

设置完成后一段时间(<24h), 你就可以在 apache 的官方页面看到你上传的 PGP [公钥](https://people.apache.org/keys/committer/jin)信息了, 然后你就无需过多的看 apache-pgp 页面里的其他说明了, 用到再说

##### 2. SVN 中设置 (可选)

这一步如果不是主**打包**的同学可以先跳过简化流程, 它的目的就是把上面每个人生成的 gpg 公钥按项目为单位归到一起, 这样就验证签名的时候就会方便许多, 然后发版/打包的同学需要添加 "dev + release" 两个 svn 分支, 相关命令如下:

```bash
# 0. mac 可 brew 安装 svn (其他官网下载), 如果 brew 国内源报错临时取消一下即可
export HOMEBREW_BOTTLE_DOMAIN=''
# 提前创建如下的文件目录
hugegraph_svn
    ├── dev      # 候选/暂存版本 (源码/二进制包等原始文件)
    └── release  # 最终版本 (投票通过后的 RC 版本，会移动到此处)


# 1.1 (在dev文件夹内) 拉取新的仓库, 第一次发版请先确保仓库存在
svn co https://dist.apache.org/repos/dist/dev/incubator/hugegraph && cd hugegraph
# 1.2 (在 release 文件夹内) 其他步骤完全一致
svn co https://dist.apache.org/repos/dist/release/incubator/hugegraph && cd hugegraph

# 2. 追加你个人的公钥到 KEYS 文件中, 下面是例子
(gpg --list-sigs xxx@apache.org && gpg --export --armor xxx@apache.org) >> KEYS
# 2.0 (仅项目初始化时需要执行，后面的Committer不需要再执行)
svn add KEYS
# 2.1 检查一下是否正确, 再上传 (首次会交互的输入user信息)
svn ci -m "add gpg public key for xxx" # xxx 对应你的 ApacheId

# 上传完后查看一下远端文件(不用传dev，系统会自动到 https://downloads.apache.org/incubator/hugegraph/KEYS )
# curl https://dist.apache.org/repos/dist/release/incubator/hugegraph/KEYS 
```

#### 3. 提交 JIRA 工单配置 Maven/SVN 权限

*这个已经完成, 无需再管, 其他同学可以跳过*

这个按照官方文档指示, 我们也需要提交一个工单申请 maven(nexus) 的权限, 这样之后项目需要被用户公开访问的 jar 包才能正常获取, 另外就是 SVN 仓库默认似乎也是不存在的, 也需要申请一下地址(建议一起申请下)

Project 选择 `Incubator`, 然后描述里主要就填写前三个参数,  SVN 地址可以直接换成 Github 地址, 第四个 `TLP project` 没太理解, 看了下似乎暂不用管 (搜索了关键词看了下其他项目提交的工单, 基本如此) [我们的 maven 工单见[INFRA-23771](https://issues.apache.org/jira/browse/INFRA-23771), SVN 工单见 [INFRA-23796](https://issues.apache.org/jira/browse/INFRA-23796)]

## 0x02. 配置 Maven 

这是一个主要的流程, 其中关键点在于第一次如何配置发版的一些用户/项目信息, 然后其中大部分是**主发版/打包**同学才需要配置的, 其他同学可跳过此章节, 或者跳过第 1 + 2 点, 并不需要全部照搬

#### 1. 配置远端 servers 地址

首先你需要先把你 apache 账户的密码进行先[加密](http://maven.apache.org/guides/mini/guide-encryption.html): (强烈建议, 但非必要)

```bash
# 1. 先要生成一个主密码管理其他密码 (建议简单点+记下免得忘了)
mvn --encrypt-master-password # 输入密码后生成一个加密串

# 2. 创建/编辑新的 sec 文件(e.g: `~/.m2/settings-security.xml`), 把刚才的加密串填入 (带大括号)
<settingsSecurity>
  <master>{avLSrXK6psMH03UPFtyGF0ZTWY9C3EEKEstfQvJho5Q=}</master>
</settingsSecurity>

# 3. 现在可以对你要加密的密码操作了 (不要直接输出明文参数, 命令行会交互即可)
mvn --encrypt-password
Password: # 这里填写你要加密的明文
# 输出的密文留着填入后面的配置文件里
```

**注:** 简单尝试可以看到 mvn 的加密相同字符串并不会输出同样的密文, 推测安全性也还可以.

然后编辑本地的 maven 配置文件 (e.g: `~/.m2/settings.xml`), 如果上面的 gpg 中私钥加了密码, 配置中就需要单独再加一个它的配置 (参考文档)。
如果 apache 账户密码做了加密处理，则 password 填加密后的密码，如果没有则直接填 apache 账户密码（若在 CI 中，则直接填 apache 账户密码）。

```xml
<!-- 建议使用默认的模板修改, 找到 servers 选项, 填写下面两个 -->
  <servers>
    <!-- Snapshot address -->
    <server>
      <id>apache.snapshots.https</id>
      <username> <!-- apache username --> </username>
      <password> <!-- apache encrypt passwd --> </password>
    </server>

    <!-- Release address -->
    <server>
      <id>apache.releases.https</id>
      <username> <!-- apache username --> </username>
      <password> <!-- apache encrypt passwd --> </password>
    </server>
  </servers>

  <!-- 可选配置 -->
  <profiles>
    <profile>
      <id>apache-release</id>
      <properties>
        <!-- Your GPG Keyname here, use "gpg --list-secret-keys --keyid-format LONG" to get keyID -->
        <gpg.keyname><!-- your gpg KEYID--> </gpg.keyname>
        <gpg.useagent>true</gpg.useagent>
        <gpg.passphrase><!-- GPG password (Optional) --></gpg.passphrase>
      </properties>
    </profile>
  </profiles>
```

#### 2.1 配置项目的 pom.xml (仅首次发版需要)

首先设置/修改 `parent` 字段, hugegraph 原有配置的是 `org.sonatype.oss`, 参考官方文档改为 (注意版本应选用官方最新值 - 2022.10)

```xml
    <parent>
      <groupId>org.apache</groupId> <!-- IDEA 内跳转可看到它的详细配置 -->
      <artifactId>apache</artifactId>
      <version>23</version>
    </parent>
```

然后官方告诉你继承它的作用是帮你默认配置了发布地址, 所以如果原 pom 里有 `distributionManagement` 配置项, 则需要移除, 这里 `hugegraph-commons` 没有, 但是有一个`scm`的配置, 不清楚是否需要移除 (暂时没动)

---

> **注:** 从这里开始, 就进入了正式的发版准备, 前面的部分可以视为 before release 章节之后拆分出去

#### 2.2 新增 release 分支发版

为了规范和方便后序打 tag, 我们在代码 PR 都合并完成后, 应该基于 master 分支 checkout 一个 `release-1.x.x` 这样的分支,
GitHub UI 界面可以直接在`branch` 输入创建基于 master 的分支, 也可以选择命令行方式:

```bash
# 0. 完成发版代码准备后, 从 master 切到一个新的发版分支, 然后后序操作都在发版分支进行. 最后也方便归档保存
git checkout -b release-1.x.x # tag 可以在 GitHub 上打
```

在上面进行发版和相关操作. 后面的 pom 修改/版本检查可以都放这个分支操作, 最后合入到 master 中.

<img src="https://github.com/apache/incubator-hugegraph/assets/79143929/b64ef189-38f2-45a3-ab48-0604d9d821ac" alt="image" style="zoom:50%;" />

发布后确认是 `pre-release` 状态, 进入下一步修改版本等操作. (注意 GitHub 可能会发送邮件广播, 慎重操作)


#### 2.3 通用 pom 修改

(非首次)平常发版主要是要修改项目的**版本号**, 若是不同模块如果有多个版本号的就比较麻烦, 建议统一为一个这样子目录就可直接继承父的, 避免忘记修改的事情, 建议使用 mvn 插件修改:

```bash
# 最新使用了 `rivison` 统一管理版本号, 不再需要下面的方式修改, 之后可以抽出
# 1. (当前在 release-1.0.0 分支) 统一修改版本号 (另建议人工检查 XXVersion 类的版本号)
mvn versions:set -DnewVersion=1.0.0 # 请特别注意, 如果子模块使用变量直接继承父版本的, 请勿手动改回

# 2. (推荐) RAT License 检查
mvn apache-rat:check # 它会在每个模块下生成一个检查文本报告, 有问题需要立刻处理
find ./ -name rat.txt -print0 | xargs -0 -I file cat file > merged-rat # 然后检查这个 merged-rat 文件

# 查看: Binaries, Archives, Unknown License 三项是否为 0, 二进制非 0 需要确认二进制文件是否有单独 license 说明和记录(见后)

mvn apache-rat:help -Ddetail=true # 可以查看详细参数, 设置一些排除文件等
```

##### 如何处理二进制文件 (重要)

如果上面 rat 插件扫描的二进制/打包文件非0, 请务必确认 `LICENSE` 和 `NOTICE` 文件中是否标注, 没有需提交 PR 补充说明. 举个例子, 可以参考此 PR

*TODO* 

#### 3. 编译测试

上面的流程走完, 就完成了一个新项目的配置了, 倒并不多, 然后测试一下签名是否可行✅

```bash
# 1. 如果测试不长, 可以携带跑一下
mvn clean install -Papache-release

# 如果测试长, 可先选择跳过
mvn clean install -Papache-release -DskipTests

# 如果 doc 插件有报错, 也可先跳过不阻塞 or maven javadoc plugin add config to ignore error
mvn clean install -Papache-release -DskipTests -Dmaven.javadoc.skip=true
## maven java-doc插件配置忽略错误: (推荐)
<configuration>
    <doclint>none</doclint>
    <failOnError>false</failOnError>
</configuration>



# 2. 尝试推送到 apache (请确保当前在 release-xx 分支), 注意此时不可跳过生成 doc
mvn clean deploy -Papache-release -DskipTests
```

可能遇到的报错如下

Q1: **maven-javadoc-plugin** 插件报错, 提示 `JAVA_HOME` 没有配置

A: 首先尝试配置系统的 `JAVA_HOME`, 然后如果仍然报错, 尝试检查你的 javadoc 版本到 3.x (低版 2.9.x有一些已知 bug), 可直接继承 parent 版本, 最后仍不行参考[SOF](https://stackoverflow.com/questions/13961615/unable-to-find-javadoc-command-maven), 最后如果发现 doc 生成有一堆报错, 建议先跳过它 (不阻塞发版主线)

Q2: 推送时提示 `401/400` 错误 (deploy)

A: 首先如果是出现 `401: Unauthorized`  多半是你 `settings.xml` 的配置都没有设置, 要么没有生效读旧配置去了, 或者是账户名错了, 执行`mvn help:effective-settings` 获得当前配置, 然后**认真检查一下**; 然后如果出现 `400: Bad Request` 至少说明配置已经有了, 但是没有正确提交成功, 或者是你提交的仓库没有在 JIRA 上申请过 Nexus 权限, 请参考上一节的工单确认

#### 4.1 处理 Staging 状态

如果上面的 `deploy` 成功了, 其实你本地的一系列 jar 包就已经上传到了 apache 的 maven 仓库, 但它只是一个 `staging` (暂存)状态, 我们用 ApacheID 登录 [staging repository](https://repository.apache.org/#stagingRepositories) 页面, 可以在这看到刚上传的仓库 然后这里之后我们要点击 `close` 让它确认**发布**才能被之后公网访问 (见4.2)

![image](https://github.com/apache/incubator-hugegraph/assets/79143929/9fa147ce-f5e0-4bd1-9693-459134ec93c7)

另外需要注意, 如果你发布多个`staging`版本, 确认新版发布无误后需要`drop`掉之前的发版才会让新版生效, 否则不会默认覆盖

很容易误解的是为啥会是关闭的选项, 而不是"确认"之类的, 这里需要单独解释下 Nexus 上[发布](https://help.sonatype.com/repomanager2/staging-releases/managing-staging-repositories)的核心流程图 (包括后面的Promote/Release/Drop含义)

#### 4.2 使用临时的包

初次发版的时候有依赖关系, 然后涉及改名等问题, 所以需要用到临时的 maven 仓库包, 这个时候需要单独配置一下 maven 配置就可访问到了

```xml
<!-- 本地的 settings.xml, 最新版已经内置于每个项目的 pom.xml 里, 使用 -P profileID 即可启用, 无需再单独添加 -->
<profiles>
  <profile>
    <id>staged-releases</id>
    <repositories>
      <repository>
        <id>staged-releases</id>
        <url>https://repository.apache.org/content/groups/staging/</url>
      </repository>
    </repositories>
  </profile>
</profiles>
```

然后确认你的 idea 编译时使用了启用这个配置, 如果还是访问不到, 试着在项目的根下执行一下 `mvn verify -Pstaged-releases` 看是否能拉取到.

另外有个问题就是如果 Gtihub 的 Action/CI 中如果想要拉取这些临时 jar 包, 那就也需要携带一下这个仓库配置, 最简单的办法是在项目的 `pom.xml` 文件中加入这个配置 (可参考 toolchain 的 pom 底), 但生产环境不太建议这样做, 会影响所有用户的拉取优先级.

**PS:** 当然还有一个更临时的做法, 就是直接从 stage 仓库中下载 jar 包然后本地手动加载 (仅供参考)

#### 5. 使用 maven-release 打包 (TODO)

因为发版相关的命名/规范等有一系列要求, 我们简单起见可使用标准的 release 插件, 通过执行 maven 命令来打出标准发版包, 这里暂时不用使用非 apache 官方的插件打包, 可跳过此步.

#### 6. 更新第三方依赖与 license

> **注**：新增依赖的 license 不能是 [ASF第三方许可证策](https://apache.org/legal/resolved.html) 中 CATEGORY X 列出的 license 类型。

然后如果在新的 PR 或者现有仓库中存在一些外部依赖, 需要遵循下面的流程进行操作: (以 `hugegraph-toolchain` 仓库为例, 其他可以参考)

1. 在 `hugegraph-dist/scripts/dependency/known-dependencies.txt` 中添加你所需要的 jar 名称+版本。
2. 在 `hugegraph-dist/release-docs/licenses/`目录下添加新增依赖的 LICENSE 文件。
3. 在 `hugegraph-dist/release-docs/LICENSE`文件中追加新增依赖的 license 信息。
4. 在 `hugegraph-dist/release-docs/NOTICE`文件中追加新增依赖的 notice 信息。（没有NOTICE 则跳过该步骤）
其中`LICENSE`和`NOTICE` 信息可以在依赖对应的代码仓库中找到。另外新增依赖所关联的依赖也需要添加LICENSE，运行 `hugegraph-dist/scripts/dependency/regenerate_known_dependencies.sh` 可以生成项目全量最新依赖列表。

比如在源码中引入了第三方文件 `ant-1.9.1.jar`
- 项目源码位于：https://github.com/apache/ant/tree/rel/1.9.1
- LICENSE 文件：https://github.com/apache/ant/blob/rel/1.9.1/LICENSE
- NOTICE 文件：https://github.com/apache/ant/blob/rel/1.9.1/NOTICE
`ant-1.9.1.jar` 的 license 信息需要在 LICENSE 文件中指定，notice 信息需要在NOTICE文件中指定。 ant-1.9.1.jar 对应的详细 LICENSE 文件需要复制到 licenses/ 目录下

另外如果是 PR 中的 CI 检查报错, 除了引入新包外, 也可能是你改变了依赖的版本/排除依赖的情况, 可以对照报错的信息和`known-dependencies.txt`进行确认调整.

#### 7. 打源文件包 (source)

apache 要求必须提供的是**源码包**, 所以这部分最关键的, 但是如果项目 `pom` 中只配置了二进制打包, 那分两个情况来说:
1. 检查当前项目 `maven-assembly-plugin` 中是否同时设置了"src + bin" 两种包的规范
   - 都设置了, 最简单, 把两个包拷贝到单独的文件夹内后续签名即可
   - 没设置 source, 那考虑使用 `git archive` 命令来进行源码打包, 可参考 [apache-release.sh](https://github.com/apache/incubator-hugegraph-commons/blob/master/hugegraph-dist/scripts/apache-release.sh) 脚本

PS: 如果项目不需要打二进制包, 则可考虑直接使用`git archive`方式进行打源码包即可.

#### 8. 文件签名

在打包完成后, 我们要对 "源码 + 二进制" 包的所有文件都进行挨个签名, 并且计算出它们的 `sha512` 摘要确保文件一致性, 这里使用脚本: (Unix 环境), 完整的打包自动打包 + 签名 + 验证脚本可参考项目中的 [apache-release.sh](https://github.com/apache/incubator-hugegraph-commons/blob/master/hugegraph-dist/scripts/apache-release.sh) 脚本 (第一次请务必手动执行) 以免出现问题

```bash
# 1. 签名
# windows 可以单独计算签名, 或是使用 git-bash 环境或更推荐 WSL2 等类 linux 环境，默认会用第一个 GPG key， 如果要制定，可以使用 --default-key xxx 选项
for i in *.tar.gz; do echo $i; gpg --armor --output $i.asc --detach-sig $i ; done

# 2. 生成校验文件
for i in *.tar.gz; do echo $i; shasum -a 512 $i > $i.sha512 ; done # 计算SHA512

# 3. 正确性检查
# 3.1 检查签名正确性 (需要出现 "Good signature" 关键字)
for i in *.tar.gz; do echo $i; gpg --verify $i.asc $i ; done
# 3.2 检查 hash
for i in *.tar.gz; do echo $i; shasum -a 512 --check  $i.sha512; done
```

## 0x03. 上传打包文件到  SVN

> 再次说明, 这里无关乎原项目是否使用 svn 托管, 而是 apache 官方使用它维护/保存发版包内容, 所以需要下载配置 (仅需要**发版 svn** 的同学看, 其他同学可跳过)

假设你本地已经配置好了 svn 环境后, 需要做以下的事项推送本地包到 apache 仓库里 (svn 本身设计你无需多了解, 使用逻辑类似 git), 完整的脚本同样可以参考项目内的 `apache-release.sh` 脚本的 step4 部分:

1. svn 切换到本地已有目录

2. 建立新的发版目录

3. 复制 source 相关的包到 svn 本地仓库

6. (可选) 如果有 binary release 可同时上传

7. 最后, 提交到 apache 远端 svn 仓库 (**注**: commit 和 push 在 svn 里是一步)

   ```bash
   # 核心就是拉取 svn 文件夹, 加入新的版本和你要上传的文件, 然后提交到一个版本/分支, 就可以了 (类似 git)
   svn add "${release_version}"
   svn status
   # 携带提交日志, 例如
   svn commit -m 'add source & binary files for hugegraph-xxx 1.0.0'
   ```

然后, 去 SVN 远端仓库检查目录存在, **校验文件**存在且内容正常(直接点击即可访问[例如](https://dist.apache.org/repos/dist/dev/incubator/hugegraph/1.0.0/apache-hugegraph-commons-1.0.0-incubating-src.tar.gz.sha512)), 我们的打包/发布流程**核心**就走完了, 接下来进入下一个大家一起验证 + 投票的阶段

## 0x04. 验证阶段

当内部的临时发布和打包工作完成后, 其他的社区开发者(尤其是 PMC)需要参与到验证环节确保某个人发布版本的"正确性 + 完整性", 这里需要**每个人**都尽量参与, 然后后序**邮件回复**的时候说明自己**已检查**了哪些项. (下面是核心项)

#### 1. 检查 hash 值

首先需要检查 `source + binary` 包的文件完整性, 通过 `shasum` 进行校验, 确保和发布到 apache/github 上的 hash 值一致 (一般是 sha512), 这里同0x02的最后一步检验.

#### 2. 检查 gpg 签名

这个就是为了确保发布的包是由**可信赖**的人上传的, 假设 tom 签名后上传, 其他人应该下载 A 的**公钥**然后进行**签名确认**, 相关命令:

```bash
# 1. 下载项目可信赖公钥到本地 (首次需要)
curl xxx >> PK
gpg --import PK
# 1.2 等待响应后输入 trust 表示信任 tom 的公钥 (其他人名类似)
gpg -edit-key tom 

# 2. 检查签名 (可用 0x03 章节的第 ⑧ 步的 for 循环脚本批量遍历)
gpg --verify xx.asc xxx-source.tar.gz
gpg --verify xx.asc xxx-binary.tar.gz # 注: 我们目前没有 binary 后缀
```

先确认了整体的完整性/一致性, 然后接下来确认具体的内容 (**关键**)

#### 3. 检查压缩包内容

这里分源码包 + 二进制包两个方面, 源码包更为严格, 挑核心的部分说 (完整的列表参考官方 [Wiki](https://cwiki.apache.org/confluence/display/INCUBATOR/Incubator+Release+Checklist), 比较长)

首先我们需要从 apache 官方的 `release-candidate` 地址下载包到本地 (地址: `dist.apache.org/repos/dist/dev/hugegraph/`)

##### A. 源码包

解压 `xxx-hugegraph-source.tar.gz`后, 进行如下检查:

1. 文件夹都带有 `incubating`, 且不存在**空的**文件/文件夹
2. 存在`DISCLAIMER`文件
3. 存在 `LICENSE` + `NOTICE` 文件并且内容正常
4. **不存在**任何二进制文件
5. 源码文件都包含标准 `ASF License` 头 (这个用插件跑一下为主)
6. 检查每个父/子模块的 `pom.xml` 版本号是否一致 (且符合期望)
7. 检查前 3 ~ 5 个 commit 提交, 点进去看看是否修改处和源码文件一致
8. 最后, 确保源码可以正常/正确编译 (然后看看测试和规范)

```bash
# 同时也可以检查一下代码风格是否符合规范, 不符合的可以放下一次调整
mvn clean test -Dcheckstyle.skip=false
```

##### B. 二进制包

解压 `xxx-hugegraph.tar.gz`后, 进行如下检查:

1. 文件夹都带有 `incubating`
2. 存在 `LICENSE` + `NOTICE` 文件并且内容正常
3. 通过 gpg 命令确认每个文件的签名正常

**注:** 如果二进制包里面引入了第三方依赖, 则需要更新 LICENSE, 加入第三方依赖的 LICENSE; 若第三方依赖 LICENSE 是 Apache 2.0, 且对应的项目中包含了 NOTICE, 则还需要更新我们的 NOTICE 文件

#### 4. 检查官网以及 github 等页面

1. 确保官网至少满足 [apache website check](https://whimsy.apache.org/pods/project/hugegraph), 以及没有死链等
2. 更新**下载链接**以及版本更新说明
3. …..

## 0x05. 投票

因为是初次投票, 参考已有经历, 一般是**两轮** (社区内 + 基金会), 耗时至少是**一周**, 所以请务必提前准备好相关事项, 避免最终发版延期

1. HugeGraph 社区内, 发起邮件到 `dev@hugegraph.apache.org`, 然后经过 72h + 3个以上 binding (PPMC) 邮件投票 +1 即可
2. Apache 社区内, 发起投票邮件到 `general@incubator.apache.org`, 然后经过 72h + 3个以上 binding (IPMC) 邮件投票 +1 即可

PS: 篇幅原因, 邮件模板参考 [WIKI](https://github.com/apache/incubator-hugegraph/wiki/Release-Voting)

**注:**  Gmail/Live/QQ 邮件中使用**纯文本**模式编辑 (而不要选择 HTML 模式), 以免被拒或者过滤

## 0x06. 正式发布

下列流程中部分可略, 后序会调整更新:

#### A. 正式发版

1. (**重要/谨慎**) 将 [dev](https://dist.apache.org/repos/dist/dev/hugegraph) 目录下的发布包移动到 [release](https://dist.apache.org/repos/dist/release/hugegraph) 目录下，KEYS 有更新的，也需要同步更新 (命令参考如下, 版本变量自行传入, 熟悉后可考虑 CI 方式)

   ```bash
   # 从 dev 移动文件夹到 release (请注意避免冲突, 有冲突则先缺点好再删除+移动)
   svn mv https://dist.apache.org/repos/dist/dev/incubator/hugegraph/${release_version} \
     https://dist.apache.org/repos/dist/release/incubator/hugegraph/${release_version} \
     -m "move packages for release ${release_version}" 
   
   # (可选) 1.1 如果 KEYS 不一致, 则需更新 release 的 KEYS
   svn rm https://dist.apache.org/repos/dist/release/incubator/hugegraph/KEYS -m "delete KEYS" 
   # (可选) 1.2 复制过去
   svn cp https://dist.apache.org/repos/dist/dev/incubator/hugegraph/KEYS \
     https://dist.apache.org/repos/dist/release/incubator/hugegraph/ \
     -m "copy KEYS for release ${release_version}"
   ```

2.  (检查)确认 [dev](https://dist.apache.org/repos/dist/dev/incubator/hugegraph) 不存在**旧版本**的包, 以及 [release](https://dist.apache.org/repos/dist/release/incubator/hugegraph) 目录下上一个版本的发布包，这些包会被自动保存在 [这里](https://archive.apache.org/dist/incubator/hugegraph) 

3. 将 GitHub 上的仓库的[pre-release notes](https://github.com/apache/hugegraph/releases) 改为正式版本 (包括 server/toolchain/commons)

4. (可选) 修改 GitHub 的 `README`，将版本号更新到最新发布的版本, 以及增加说明/修复联系方式等

5. (**重要**) 在官网下载[中英文页面](https://hugegraph.apache.org/docs/download/download/)上提交 PR 加入最新版本的**二进制/源码包**下载链接 (注意确认**有效**避免 404)

6. 在 [actions](https://github.com/hugegraph/actions/actions) 仓库发布 tag 对应的容器镜像到 DockerHub 上

7. (可选) 有必要可合并/删除相应 release 分支, 无必要则跳过此步 (注意 `tag` 不可删)

8. 最后才能把 maven 仓库里处于 `staging` 状态的包点击 `release` 转换为正式发布 (需要等待 **24h** 同步, 此后任何人都可以直接下载访问)

#### B. 邮件告知 + 更新发版信息

在发版流程走完, 确认 maven/download 等都可以正常下载使用后, 发一封通知邮件告诉大家**发版正式完成** (不用投票)

发邮件到 `general@incubator.apache.org` 抄送 `dev@hugegraph.apache.org` 宣布正式 release (邮件模版参考社区, 待补)



下载原有 `clutch status` 信息, 并进行更新, 生效后可在[clutch](https://incubator.apache.org/clutch/hugegraph.html)和[projects](https://incubator.apache.org/projects/hugegraph.html)页面查询 (**注:** 新的 PMC/Committer 等也需要及时更新到页面上)

```bash
# 1. 下载记录状态变更の文件
svn co https://svn.apache.org/repos/asf/incubator/public/trunk/content/projects/

# 2. 修改编辑 (注意下面内容仅供参考, 请勿照搬)
<section id="News">
      <title>News</title>
      <ul>
        <li>2022-04-xx Project enters incubation.</li>
        <li>2022-12-xx First Apache HugeGraph release v1.0.0</li>
        <!--li>2023-01-xx New Committer: Imba Tom</li-->
      </ul>
</section>

# 3. 提交 (之后去 incubator.apache.org/clutch/hugegraph.html 检查生效)
svn commit -m "update release info for hugegraph ${release_version}"
```

至此, 所有流程走完, 一次完整的发版流程就告一段落, 后续如果发现发版内容有严重问题, 可参考打补丁流程. (另外, 官方**公众号** 的文章更新请务必提前准备)

---

**参考资料:**

1. [Publishing Maven Releases to Maven Central Repository - Apache INF](https://infra.apache.org/publishing-maven-artifacts.html?spm=a2c6h.12873639.article-detail.9.77847966xfoBK5)
2. [座谈会：Apache 基金会那些事儿 - 发版部分](https://www.infoq.cn/article/cjpgokvphzkhn*vw4enk)
3. [如何准备 Apache Release - Dubbo - CN](https://dubbo.apache.org/zh/blog/2018/09/02/%E5%A6%82%E4%BD%95%E5%87%86%E5%A4%87apache-release/)
4. [Apache ServiceComb 发版指南 - CN](https://servicecomb.apache.org/cn/developers/release-guide/) 
5. [如何发布 Apache Linkis - CN](https://linkis.incubator.apache.org/zh-CN/community/how-to-release/)
6. [发版流程 Apache Doris - CN](https://doris.apache.org/zh-CN/community/release-and-verify/release-prepare)
7. 更新 ing….