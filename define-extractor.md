# 定义 Extractor 

在 Scala 中 Extractor 为定义了 unapply 方法的对象。unapply 的作用是匹配一个值，然后从中提取所需的部分。通常 unapply 和 apply 一起定义，但这不是必须的。例如，前面定义上篇文章中所需的 Email 对象。

```
object EMail {
	def apply(user:String,domain:String) = user + "@" + domain
	def unapply(str:String) :Option[(String,String)] ={
		val parts = str split "@"
		if(parts.length==2) Some(parts(0),parts(1)) else None
	}
}
```

Email 对象定义了 apply 和 unapply 方法，apply 方法没有什么特别之处，而 unapply 方法则把 EMail 对象变成了一个 Extractor。在某种程度上来说，unapply 和 apply 的作用相反，apply 方法把两个字符串参数变成一个 Email 地址，而u napply 方法反向变换，把一个字符串分解成两个部分：用户名和域名。unapply 方法必须处理不能把一个字符串分解成两部分的情况，这也是为什么这里 unapply 方法返回 Option 类型的原因。

简单的测试如下：

```
scala> EMail.unapply("james@guidebee.com")
res1: Option[(String, String)] = Some((james,guidebee.com))
```

```
scala> EMail.unapply("James Shen")
res2: Option[(String, String)] = None
```

现在，模式匹配碰到一个模式引用到一个 Exactor 对象时，会调用该对象的 unapply 方法，比如:

```
selectorString match { case EMail(user,domain) => ...
```

会调用

```
EMail.unapply(selectorString)
```

例如：

```
"james.shen@guidebee.com" match{
	case EMail(user,domain) => println (user +" AT " + domain)
	case _ =>
}
```

```
james.shen AT guidebee.com
```
