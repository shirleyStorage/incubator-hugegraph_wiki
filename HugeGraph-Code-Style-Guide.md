> Note: 加入 Apache (ASF) 后, 为了尽量降低用户提交代码的门槛以及减少不必要的风格检查 review, 注意**换行/对齐**时应尽量使用 IDE 推荐/自动的格式化, 减少以前大量的"**手动**"格式化, 因为这会给新用户带来很大的格式化困扰 (由于存在手动格式化与自动格式化产生冲突)
>
> 尤其要避免/减少无法**定义规则**的对齐, 我们的目标是尽可能让 `95%+` 的代码可以自动被格式化, 极少数历史遗留代码需要思考/手动换行, 但仍不应该使用**特殊人工** format 的规则, 后文中以此为基准再进行理解 (不确定度可以联系 PMC 同学)

### 1. 行长度

建议单行 `≤100`, IDEA 强烈建议导入项目根下的 `hugegraph-style.xml` 配置文件进行格式化

> Eclipse、Idea 等 IDE 均可针对项目进行设置，下文所述的换行对齐规则一般 IDE 也可设置。

**好处：**
- 便于阅读
- 命令行及 Github 可一行容纳下
- 符合流行社区习惯


### 2. 对齐与缩进
对于字符串和函数参数，对齐优先级高于缩进。也就是说换行时，如果能与上一行同类代码对齐则尽量对齐。(不强制, 请勿使用**人工**且无法被 IDE 自动 format 的对齐规则)

注意: 下面的所有 case 都是当前行 `> 100` 字符的时候才考虑执行, 如果没有超过则优先一行, 历史 80 字符换行的, 看到可以合并到一行 (也可以 ignore)


#### 2.1 多条件 if 语句换行对齐


这里为了美观忽略内部规范：多条件并列时，建议逻辑运算符写在行尾，下行条件与上行括号对齐。如：


```java
if (query.condition(HugeKeys.SOURCE_VERTEX) != null &&
    query.condition(HugeKeys.DIRECTION) != null &&
    !keys.isEmpty() && query.matchUserpropKeys(keys)) {
```

#### 2.2 长函数参数换行对齐

建议括号内的逗号处换行。如：

*注：不同浏览器上对齐效果可能不一致，两个 Set 首字母是竖直对齐的。*

```java
protected void prepareAdditions(Set<HugeVertex> updatedVertexes,
                                Set<HugeEdge> updatedEdges) {
```

另外 throws 换行对齐是与函数参数保持一致。

#### 2.3 函数调用换行参数对齐

建议括号内的逗号处换行，如：

```java
instance.longFunction(param1,
                      param2);
```

```java
instance.longLongFunction(
         param1,
         param2);
```

#### 2.4 长字符串换行对齐

建议在明显分隔符出换行，如逗号后，且空格保留在上行。如果能在第二行容纳得下所有内容，则第一行空着以保留完整字符串。如：

```java
throw new BackendException(
          "Not support querying by multi edge-labels");
```

#### 2.5 三元表达式换行对齐

保持与条件判断语句风格一致，符号写在行末，两个分支都另起一行

```java
Number n1 = (first instanceof Number) ?
            (Number) first :
            toBig.apply(first);
```

### 3. 空行

为了直观区分各个代码块，如下情况需要添加空行：

- package 下一行
- class、enum 定义的前一行和后一行
- 成员变量定义完成与方法定义开始之间加空行
- 方法定义、内部类定义、静态代码块等，它们之间加空行
- 文件末尾保持一行空行
- 方法内部逻辑相对独立的代码块使用空行分隔

### 4. 变量、方法命名

- 变量、方法命名：驼峰命名，保持与 Google 规范一致即可。
- 方法前缀：方法名不需要 get、set、is 前缀，以保持方法名称的简洁。

### 5. 逻辑 if-return 问题

关于 if-return-else-return 语句的清理咱们遵循的原则是：简短的错误处理代码放在方法体前部，if 后直接 return，后面不接 else；如果是并列的业务逻辑代码，可使用 if-return else-if-return else-return 以使得逻辑更加清晰；关于是否判断为并列逻辑，如果没有明显的边界，那么倾向使用原则 1。

> 1. https://softwareengineering.stackexchange.com/questions/157407/best-practice-on-if-return
>
> 2. https://blog.mozilla.org/nnethercote/2009/08/31/no-else-after-return-considered-harmful/

### 6. 注释风格

- 注释写在代码上面，不要写在代码右边或下面 (不使用行尾注释)

- 一行注释用`//`，多行注释用`/**/`

- 类变量/方法的注释都使用 java-doc 的写法，即： 

```java
    /**
     * xxx
     */
```

- 方法内的注释使用首行单星号的多行注释，即：

```java
    /*
     * xxx
     * xx
     */
```

### 7. 错误检查

错误检查建议使用：`E.checkArgument()`、`E.checkState()` 等工具函数代替 `if` + `throw` 的方式。

```java
E.checkArgument(!primaryKeyIds.contains(propKey.id()),
                "Can't remove primary key '%s'", propKey);
 
E.checkState(prop.element() != null,
             "No owner for removing property '%s'", prop.key());
```

> 事实上 `org.apache.hugegraph.util.E` 直接调用 `com.google.common.base.Preconditions`，错误检查需频繁使用，使用 "E" 代替 "Preconditions" 的原因是为了减少代码的长度。


### 8. 异常处理

异常的处理一般在最上层调用处进行，或者外部 API 调用处进行。

如果进行异常转换时，建议通过日志打印原始异常的堆栈信息，或者将原始异常作为新异常的 cause 以记录异常栈。

```java
try {
    return new GraphTransaction(this, this.loadGraphStore());
} catch (BackendException e) {
    String message = "Failed to open graph transaction";
    LOG.error("{}", message, e);
    throw new HugeException(message);
}
```

不允许存在吞异常的行为，吞异常会导致上层逻辑混乱：

```java
try {
    return new GraphTransaction(this, this.loadGraphStore());
} catch (BackendException e) {
    e.printStackTrace();
}
```

> 如果明确的可以忽略异常，请使用 "ignored" 进行说明 `try {...} catch (XxException ignored) {}`。


### 9. 日志打印

日志实例统一使用 `LOG` 命名，通过 `Log.logger()` 获取：

```java
private static final Logger LOG = Log.logger(HugeGraph.class);
```

日志打印需要 "{}" 作为填充，而不是使用字符串拼接。

```java
LOG.info("Opening backend store '{}' for graph '{}'",
         backend, this.name);
```

> 事实上 `org.apache.hugegraph.util.Log` 直接调用 `org.slf4j.LoggerFactory`，日志需频繁使用，使用 "Log" 代替 "LoggerFactory" 的原因依然是为了减少代码的长度。


### 10. 字符串拼接

建议优先使用`String.format()`进行字符串拼接，以增强代码可读性。
```java
String message = String.format("Graph '%s' start backup!", this.graph);
```

不允许在打印日志或错误检查时拼接字符串，以避免无谓的浪费。
```java
LOG.debug("Do query: " + query);
E.checkArgument(!query.empty(), "The query is empty: " + query);
```


### 11. 其它
其它未提及方面请以 [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html) 为准。