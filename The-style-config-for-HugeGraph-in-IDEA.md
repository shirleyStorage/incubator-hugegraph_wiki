> (EN version) The background is the configuration and description under IDEA. For non-IDEA, you need to consider other methods temporarily, or provide similar documents for reference (You can update to the official website with PR, THX)

## 0x00: Github PR/Git Process/Usage

1. It is recommended that both beginners and experienced users try to use the [Desktop](https://desktop.github.com/) desktop end launched by GitHub official, which greatly simplifies and integrates git usage. A brief document will be written later for explanation.
2. It is recommended to install and enable the [Refined Github](https://github.com/refined-github/refined-github) plugin in the browser. After binding the personal GitHub token, the full function is turned on, further optimizing various usage details, and the usage is smoother (Issue/PR/CI/conflicts, etc.)
3. How to configure and start the [server environment(win/unix)](https://hugegraph.apache.org/cn/docs/contribution-guidelines/hugegraph-server-idea-setup/) in local `IDEA`
4. **PR Title** submission specification refers to [Google-Conventional-Commit](https://www.conventionalcommits.org/en/v1.0.0/#summary)
   - We simplifies it based on it and removes the extended tag such as `style/ci/build/perf/test`
   - Only retains the **core** `feat/fix/chore/doc/refact/BREAKING CHANGE` (except for exclusive nouns, all use **lowercase letters**)
 `e.g: fix(server): NPE when query Gremlin`
5. You can read and learn the article [Some Tips for Using GitHub(CN)](https://xuanwo.io/reports/2022-32/) (if you aren't familiar with it)


## 0x01: General Configuration

- First confirm that the preparatory work/configuration files, etc. are completed, and then start to modify and adjust. At this time, you can get multiple benefits, adjust multiple parts at the same time, and the efficiency is much higher
  - First import the IDEA-specific [hugegraph-style.xml](https://github.com/apache/incubator-hugegraph/blob/master/hugegraph-style.xml) code style configuration (**must**)
  - Install [autocorrect](https://plugins.jetbrains.com/plugin/20244-autocorrect) + [grammar check](https://github.com/apache/incubator-hugegraph-doc/pull/282#issuecomment-1719156940)(grazie) plugins in the IDEA store, and turn on the use in the [options](https://github.com/apache/incubator-hugegraph-doc/pull/282#issuecomment-1719156940), automatically process typesetting comments/Chinese and English/spaces/punctuation, etc.)
  - Check the "Auto Import" option to ensure that "Add on the fly" + "Optimize on the fly" is turned on✓
  - Create a "Copyright Profile" in the IDEA settings, copy the community license header comment text configuration (also select it in "profile" as your **default copyright** settings)
  - Please note the configuration of IDEA's "**Actions on save**" function, which can do multiple things at one time when (automatically) saving files (but still need to check again)
    - It is recommended to check "Reformat Code" + "Optimize imports" + "Run code cleanup" + "Update copyright", but pay attention to testing and checking for errors first ("Rearrange code" needs to pay attention to some old codes, or temporarily turn off if there are many adjustments found)
    - The "clean code" place can help us avoid a lot of problems that do not meet the `code` requirements. Note that some IDEA are just warnings (default not modified), but *code is mandatory, it is recommended to **configure** to modify (for example, if missing parentheses)
  - Please ensure that all files in the entire warehouse are "LF" **line breaks**
    - If there is non-compliance, [format first](https://codeantenna.com/a/afhmHjwAjT), and then make the first **commit**, otherwise the subsequent package name will be identified as **the entire file** modification (important)
    - It is recommended to set git to prohibit **mixed line breaks** to submit `git config --global core.safecrlf true` (and set to automatically convert LF line breaks when submitting `git config --global core.autocrlf input`)
    - A better way is to configure the `.gitattributes` file at the root of the project, and set the submission line break of this project on any platform in it (recommended)

> Ordinary students/devs could **stop here**, after the above operations are completed, you can quickly start HG's code reading/development

## 0x02: Code Clean

- Basic formatting + clean work (use IDEA plugin to assist, **module --> project** level right-click `Reformat Code/Analyze Code`, etc.)
  - Confirm that the package name import order is correct (if Step1 has been done, just check again here)
  - Basic line break/align problems (the same, use [hugegraph-style.xml](https://github.com/apache/incubator-hugegraph/blob/master/hugegraph-style.xml) for batch formatting)
  - Deal with the obviously **non-compliant** parts in the code specification, it is **strongly recommended** to use IDEA's `Code | Analyze Code | Run Inspection by name` to handle it with one click
    - if/while/for missing parentheses
    - missing override
    - Explicit type can be replaced with '<>' 
    - ..... (can **scan globally** all the same problems)
  - Delete files that the community does not need (including internal files)
  - Delete empty files/empty folders, etc. (script check)
  - Key functions/paths **lack of testing / lack of documentation / need to refactor**, need to add "TODO: need test/doc/refactor" and other identification words at the beginning of the class
  - Delete **useless/outdated** code (confirm that it is no longer needed, be cautious and uncertain can mark "TODO")
  - Other basic obvious problems that affect reading/merging etc.

---

Appendix: (ASF standard header, new files should adopt this typesetting, some old typesetting line alignment is not reasonable, should be replaced uniformly
```java
/*
 * Licensed to the Apache Software Foundation (ASF) under one or more
 * contributor license agreements.  See the NOTICE file distributed with
 * this work for additional information regarding copyright ownership.
 * The ASF licenses this file to You under the Apache License, Version 2.0
 * (the "License"); you may not use this file except in compliance with
 * the License.  You may obtain a copy of the License at
 *
 *     http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */
```


---

> (CN version) 背景是 IDEA 下的配置和说明, 非 IDEA 暂时需要考虑其他方式, 或者类似提供文档供参考 (后续小伙伴可以更新到官网)

## 第零步: Github PR/Git 流程/使用

1. 推荐新手/熟手都可以直接尝试使用 GitHub 官方推出的 [Desktop](https://desktop.github.com/) 桌面端, 大幅简化和一体化 git 使用, 之后需要会单独写个简短文档说明
2. 推荐浏览器安装启用 [Refined Github](https://github.com/refined-github/refined-github) 插件, 绑定个人 GitHub token 后开启完整功能, 进一步优化各种使用细节, 使用更加顺滑 (Issue/PR/CI/conflicts等)
3. 本地 `IDEA` 如何配置启动 [server 环境(win/unix)](https://hugegraph.apache.org/cn/docs/contribution-guidelines/hugegraph-server-idea-setup/)  
4. **PR Title** 提交规范参考 [Google-Conventional-Commit](https://www.conventionalcommits.org/zh-hans/v1.0.0/#%E6%A6%82%E8%BF%B0)
   - 社区基于它简化去掉了 `style/ci/build/perf/test` 这些扩展 tag
   - 只保留/使用**最核心**的 `feat/fix/chore/doc/refact/BREAKING CHANGE` (除专属名词外均采用**小写字母**)
 `e.g: fix(server): NPE when query Gremlin`
5. 可阅读学习 [GitHub 使用的一些小技巧](https://xuanwo.io/reports/2022-32/) 文章


## 第一步: 通用配置

- 先确认准备工作/配置文件等做完, 然后再开始修改调整, 此时可以一举多得, 同时调整多个部分, 效率高许多
  - 首先导入 IDEA 专属  [hugegraph-style.xml](https://github.com/apache/incubator-hugegraph/blob/master/hugegraph-style.xml) code style 配置 (**务必**)
  - IDEA 商店安装 [autocorrect](https://plugins.jetbrains.com/plugin/20244-autocorrect) + [语法检查](https://github.com/apache/incubator-hugegraph-doc/pull/282#issuecomment-1719156940)(grazie) 插件, 并在[选项中](https://github.com/apache/incubator-hugegraph-doc/pull/282#issuecomment-1719156940)开启使用, 自动处理排版注释/中英文/空格/标点符号等)
  - 检查"Auto Import" 选项, 确保 "Add on the fly" + "Optimize on the fly" 开启✓
  - IDEA 设置中新建 "Copyright Profile", 复制社区 license header 注释文本配置后, 先单文件/小范围测试再铺开到模块级别
    - 请在 "Formatting" 配置中确认**不同类型**的文件的**注释**格式 (例如 Java/JS/Shell/SQL/XML 等, 勿直接使用默认值, 以**社区对应类型文件**作为参考标准确认)
  - 请注意配置 IDEA 的 "**Actions on save**" 功能, 可一次在(自动)保存文件时做多个件事 (但仍需再次检查)
    - 建议勾选 "Reformat Code" + "Optimize imports" + "Run code cleanup" + "Update copyright", 但要注意先测试检查无误 ("Rearrange code"在旧代码需要注意一些, 或者是发现调整的地方很多就临时关闭)
    - 格式化建议**提前排除** protobuf 生成的文件, 以及不需要/不应该格式化的文件 (如果不好排除, 则应暂时停用 Actions on save 的部分功能单独处理)
    - "clean code" 的地方可以帮我们避免大量不符合 `code`要求的问题, 注意有些 IDEA 只是警告(默认不修改), 但 *code 是强制, 建议**配置**为修改 (例如 if 缺括号)
  - 请先确保全仓库所有文件均为 "LF" **换行符**
    - 如果存在不符合的[先格式化](https://codeantenna.com/a/afhmHjwAjT), 然后进行第一次**提交**, 否则后续换包名后会被识别为**整个文件**修改 (重要)
    - 建议 git 设置禁止**混合换行符**提交 `git config --global core.safecrlf true` (以及设置提交时自动转换 LF 换行符 `git config --global core.autocrlf input`)
    - 更好的做法是在项目根下配置`.gitattributes`文件, 在里面统一设定本项目的任意平台下提交换行符 (推荐)

> 普通同学到此止步, 上述操作完就可以快速开始 HG 的代码阅读/开发了

## 第二步: 代码 clean

- 基本的格式化 + clean 工作 (使用 IDEA 插件辅助, **模块 --> 项目**级别右键 `Reformat Code/Analyze Code`等)
  - 确认包名 import 顺序正确 (若 Step1 已做, 则此处只需再次检查即可)
  - 基本的换行/align 的问题 (同上, 使用[ hugegraph-style.xml](https://github.com/apache/incubator-hugegraph/blob/master/hugegraph-style.xml) 进行批量格式化)
  - 处理 code 规范中明显**不合规**的部分,  **强烈建议**优先使用 IDEA 的 `Code | Analyze Code | Run Inspection by name` 一键处理
    - if/while/for 缺括号
    - missing override
    - Explicit type can be replaced with '<>' 
    - ..... (可以**全局扫描**所有问题相同问题)
  - 删除社区不需要的文件 (包括内部文件)
  - 删除空文件/空文件夹等 (脚本检查)
  - 关键功能/路径**缺乏测试 / 缺乏文档 / 需要重构**的, 需要在类开头加上 "TODO: need test/doc/refactor" 等标识字样
  - 删除**无用的/过时**的代码 (确认不再需要的, 谨慎不确定的可以标记"TODO")
  - 其他基本的明显影响阅读/合并的问题 etc.

---

附录: (ASF 标准头, 新的文件都应该采用这个排版, 有些旧排版的换行对齐不太合理, 应该予以统一替换
```java
/*
 * Licensed to the Apache Software Foundation (ASF) under one or more
 * contributor license agreements.  See the NOTICE file distributed with
 * this work for additional information regarding copyright ownership.
 * The ASF licenses this file to You under the Apache License, Version 2.0
 * (the "License"); you may not use this file except in compliance with
 * the License.  You may obtain a copy of the License at
 *
 *     http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */
```