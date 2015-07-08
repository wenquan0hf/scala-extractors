# 正规表达式 

Extractor 一个特别有用的应用是正规表达式，Scala 支持正规表达式，尤其是和 Extractor 配合使用时显得非常便利。

## 生成正规表达式 

Scala 继承了 Java 的正规表达式的语法规则。这里我们假定你了解正规表达式。Scala 的正规表达式相关的类型定义在包 scala.util.matching 中。

创建一个正规表达式是使用 RegEx 类型，例如：

```
scala> import scala.util.matching.Regex
import scala.util.matching.Regex
```

```
scala> val Deciaml = new Regex("(-)?(\\d+)(\\.\\d*)?")
Deciaml: scala.util.matching.Regex = (-)?(\d+)(\.\d*)?
```

这里构建了一个可以识别数值的正规表达式。这个表达式中使用多个\\转义，对于复杂的正规表达式来说，有时显得太复杂，很容易出错，Scala 允许你使用原始的正规表达式（无需转义），这可以通过将正规表达式包含着”””字符串中，比如重写上面的正规表达式如下：

```
val Deciaml = new Regex("""(-)?(\d+)(\.\d*)?""")
```

此外还有一个更简洁的方法，是在字符串后使用.r 操作符，例如：

```
scala> val Deciaml = """(-)?(\d+)(\.\d*)?""".r
Deciaml: scala.util.matching.Regex = (-)?(\d+)(\.\d*)?
```

.r 为 StringOps 的方法，它把一个字符串转换为 Regex 对象。

## 使用正规表达式查找字符串 

scala 支持下面几种正规表达式的查找方法：

- regex findFirstIn Str 查找第一个匹配的字符串，返回 Option 类型  
- regex findAllIn str 查找所有匹配的字符串，返回 Interator 类型  
- regex findPrefixOf str 从字符串开头检查是否匹配正规表达式，返回 Option 类型。  

例如：

```
scala> val Decimal = """(-)?(\d+)(\.\d*)?""".r
Decimal: scala.util.matching.Regex = (-)?(\d+)(\.\d*)?
```

```
scala> val input = " for -1.0 to 99 by 3"
input: String = " for -1.0 to 99 by 3"
```

```
scala> for(s <- Decimal findAllIn input) println(s)
-1.0
99
3
```

```
scala> Decimal findFirstIn input
res5: Option[String] = Some(-1.0)
```

```
scala> Decimal findPrefixOf input
res6: Option[String] = None
```

## 使用正规表达式分解数据 

Scala 所有定义的正规表达式都定义了一个 Extractor，可以用来解析正规表达式中对应的分组，比如前面定义的Decimal定义了三个分组，可以直接用来解析一个浮点数：

```
scala> val Decimal(sign,integerpart,decimalpart) = "-1.23"
sign: String = -
integerpart: String = 1
decimalpart: String = .23
```

如果对应的分组查找不到，则返回 Null，比如：

```
scala> val Decimal(sign,integerpart,decimalpart) = "1.0"
sign: String = null
integerpart: String = 1
decimalpart: String = .0
```

这种分解方法同样可以应用到 for 表达式中，例如：

```
for(Decimal(s,i,d) <- Decimal findAllIn input) 
	println ("sign: " +s + ",integer:" +
	i + ",deciaml:" +d)
```

```
sign: -,integer:1,deciaml:.0
sign: null,integer:99,deciaml:null
sign: null,integer:3,deciaml:null
```
