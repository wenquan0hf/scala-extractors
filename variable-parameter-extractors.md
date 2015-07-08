# 可变参数的 Extractors #
前面的几个例子中 Extractor 返回的结果的数目都是固定的，比如 EMail 返回两个：用户名和域名。有些时候，这显得有些不够灵活。比如你打算匹配一个域名，而返回的部分为域名的各个部分，你可能会写如下的模式：

```
dom match{
	case Domain("org","acm") => print("acm.org")
	case Domain("com"，"sun","java") => println("java.sun.com")
	case Domain("net",_*) => println (" a .net domain")
}
```

这个例子的模式定义有很大的局限性，只能匹配 acm.org，java.sun.com 和*.net 域名。问题是我们如何实现可以匹配任意类型的域名，并分解出域名的各个部分。

针对这种变长类型的匹配，Scala 定义了一个 unapplySeq 方法来支持这种用法，例如：

```
object Domain{
	def apply(parts:String *) :String = parts.reverse.mkString(".")
	def unapplySeq(whole:String): Option[Seq[String]] =
		Some(whole.split("\\.").reverse)
}
```

对象 Domain 定义 unapplySeq 方法，首先以“.”分割字符串，Split 使用正规表达式，因此.需要使用\\\转义。unapplySeq 结果返回一个封装在 Some 的 Seq 数据。

然后你可以使用 Domain Extractor 来获取 Email 地址更详细的信息。比如查找用户名为“tom”，域名为某些含“com”的地址。

```
def isTomDotCom(s:String):Boolean =s match{
	case EMail("tom",Domain("com",_*)) => true
	case _ => false
}
```

测试如下：

```
scala> isTomDotCom("tom@sun.com")
res0: Boolean = true
```

```
scala> isTomDotCom("peter@sun.com")
res1: Boolean = false
```

```
scala> isTomDotCom("tom@acm.org")
res2: Boolean = false
```

使用 unapplySeq 也支持返回一部分固定长度的变量加上后面变长的变量，这个返回值可以表示成一多元组，可变的部分放在最后。比如：

```
object ExpendedEMail{
	def unapplySeq(email: String)
		:Option[(String,Seq[String])] ={
		val parts = email split "@"
		if(parts.length==2)
			Some(parts(0),parts(1).split("\\.").reverse)
		else
			None
	}
}
```

本例的 unapplySeq 返回一个二元组，第一个元素为用户名，第二个元素为一个 Seq 包含域名的所有部分。

```
scala> val s ="james.shen@mail.guidebee.com"
s: String = james.shen@mail.guidebee.com
```

```
scala> val ExpendedEMail(name,topdomain,subdoms @ _*) =s
name: String = james.shen
topdomain: String = com
subdoms: Seq[String] = WrappedArray(guidebee, mail)
```
