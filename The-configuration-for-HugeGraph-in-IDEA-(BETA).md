> 背景是 IDEA 下的配置和说明, 非 IDEA 暂时需要考虑其他方式, 或者类似提供文档供参考

## 第一步: 通用配置

- 先确认准备工作/配置文件等做完, 然后再开始修改调整, 此时可以一举多得, 同时调整多个部分, 效率高许多
  - 首先导入 IDEA 专属  [hugegraph-style.xml](https://github.com/apache/incubator-hugegraph/blob/master/hugegraph-style.xml) code style 配置 (**务必**)
  - 检查"Auto Import" 选项, 确保 "Add * on the fly" + "Optimize * on the fly" 开启✓
  - IDEA 设置中新建 "Copyright Profile", 复制社区 license header 注释文本配置后, 先单文件/小范围测试再铺开到模块级别
    - 请在 "Formatting" 配置中确认**不同类型**的文件的**注释**格式 (例如 Java/JS/Shell/SQL/XML 等, 勿直接使用默认值, 以**社区对应类型文件**作为参考标准确认)
  - 请注意配置 IDEA 的 "**Actions on save**" 功能, 可一次在(自动)保存文件时做多个件事 (但仍需再次检查)
    - 建议勾选 "Reformat Code" + "Optimize imports" + "Rearrange code" + "Run code cleanup" + "Update copyright", 但要注意先测试检查无误
    - 格式化建议**提前排除** protobuf 生成的文件, 以及不需要/不应该格式化的文件 (如果不好排除, 则应暂时停用 Actions on save 的部分功能单独处理)
    - "clean code" 的地方可以帮我们避免大量不符合 `code`要求的问题, 注意有些 IDEA 只是警告(默认不修改), 但 icode 是强制, 建议**配置**为修改 (例如 if 缺括号)
  - 请先确保全仓库所有文件均为 "LF" **换行符**
    - 如果存在不符合的[先格式化](https://codeantenna.com/a/afhmHjwAjT), 然后进行第一次**提交**, 否则后续换包名后会被识别为**整个文件**修改 (重要)
    - 建议 git 设置禁止**混合换行符**提交 `git config --global core.safecrlf true` (以及设置提交时自动转换 LF 换行符 `git config --global core.autocrlf input`)
    - 更好的做法是在项目根下配置`.gitattributes`文件, 在里面统一设定本项目的任意平台下提交换行符 (推荐)

## 第二步: 代码 clean

- 基本的格式化 + clean 工作 (使用 IDEA 插件辅助, **模块 --> 项目**级别右键 `Reformat Code/Analyze Code`等)
  - 修改完包名之后的 import 顺序 (若 Step1 已做, 则此处只需再次检查即可)
  - 基本的换行/align 的问题 (同上, 使用[ hugegraph-style.xml](https://github.com/apache/incubator-hugegraph/blob/master/hugegraph-style.xml) 进行批量格式化)
  - 删除**无用的/过时**的代码 (确认不再需要的)
  - 处理 code 规范中明显不合规的部分,  **强烈建议**优先使用 IDEA 的 `Code | Analyze Code | Run Inspection by name` 一键处理
    - if/while/for 缺括号
    - missing override
    - Explicit type can be replaced with '<>' 
    - ..... (可以**全局扫描**所有问题相同问题)
  - 删除社区不需要的文件 (包括内部文件)
  - 删除空文件/空文件夹等 (脚本检查)
  - 关键功能/路径**缺乏测试 / 缺乏文档 / 需要重构**的, 需要在类开头加上 "TODO: need test/doc/refactor" 等标识字样
  - 其他基本的明显影响阅读/合并的问题 etc.