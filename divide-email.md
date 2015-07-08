# 分解 Email地址的例子 #
前面我们介绍了使用模式匹配来分解数据，本专题教程对这些概念加以推广。到目前为止，构造器模式是和 Case Class 关联在一起的。有些时候你希望使用类似的模式而不需要创建 Case Class。实际上你可能希望创建自定义的模式。 Extractor（解析器）可以帮助你完成这些任务。本篇以一个简单的例子来介绍 Extractor 的概念和用法。

比方说给定一个代码 Email 地址的字符串，你需要判断它是否是一个有效的 Email 地址，如果是有效的 Email 地址，你需要分别取出用户名和域名两个部分。传统的实现方法可以定义如下三个辅助函数：

```
def isEmail(s:String): Boolean
def domain(s:String): String
def user(s:String): String
```

使用这些方法，你可以使用如下代码分析输入的字符串：

```
if(isEmail(s)) println(user(s) + " AT " + domain(s))
else println("not an email address")
```

这段代码功能是正确的，但比较笨拙，而且如果需要同时有多个测试时情况就变得比较复杂，比如你在一串字符串中寻找相连的同一个用户名的 Email 地址。

我们之前介绍的模式匹配处理这类问题非常有效。简单你可以使用

```
Email(user,domain)
```

来匹配一个字符串。这个模式可以匹配含有“@”有个字符串，使用这个模式，你可以使用 user，domain 分别绑定用户名和域名。我们使用模式匹配重写前面的例子：

```
s match{
	case Email(user,domain) => println (user + " AT " + domain)
	case _ => println ("not an email address")
}
```

而解决寻找相连两个同名的 Email 地址可以使用如下代码：

```
s match{
	case Email(u1,d1)::Email(u2:d2):: _ if(u1==u2) => ...
	...
}
```

这段代码看起来简单明了，但问题是 String（s 的类型)不是一个 case class。 它们不具有可以表示为 Email(user，domain）的方法。 此时我们就可以借助于 Extractor，它们支持为这些内置的类型定义新的模式。
