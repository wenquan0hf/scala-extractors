# 无参数和带一个参数的模式定义 

前面例子中的 unapply 方法在匹配成功时返回一个二元组。这可以很容易的推广到多于两个变量的情况。为了绑定N个变量，unapply 方法可以返回一个 N 元祖，封装在 Some 中。

对于一个只绑定一个变量的情况有些特殊。Scala 没有所谓“一元组”，为返回一个变量，该变量直接封装在 Some 中返回。下面的例子的 Twice 匹配一个含有两个连续的相同字符串的情况。

```
object Twice {
	def apply(s:String) = s + s
	def unapply(s:String):Option[String] ={
		val length=s.length/2
		val half = s.substring(0,length)
		if(half == s.substring(length)) Some(half) else None
	}
}
```

一个 Extractor 也可以绑定任何变量，这种情况下可以返回 true 和 false 代表匹配成功与否。比如下面的例子匹配一个字符串是否都是大写字母：

```
object UpperCase {
	def unapply(s:String):Boolean = s.toUpperCase ==s 
}
```

这个例子只定义了 unapply 方法，因为这里定义 apply 方法无任何意义。

之后我们可以利用上面定义的 Twice 和 UpperCase Extractor 组合构造组合的模式定义（还记得之前提到的 Monoid，设计模式 Option 类型就是一个 Monoid，Monoid 一个特点就是可以任意组合）。

```
def userTwiceUpper(s:String) = s match{
	case EMail(Twice(x @ UpperCase()),domain) =>
		"match:" + x + " in domain " + domain
	case _ => "no match"
}
```

第一个模式定义去匹配所有的 Email 地址，这个 Email 地址的用户名为大写，并且有两个相同的字符串构成。例如：

```
scala> userTwiceUpper("DIDI@hotmail.com")
res1: String = match:DI in domain hotmail.com
```

```
scala> userTwiceUpper("DIDO@hotmail.com")
res2: String = no match
```

```
scala> userTwiceUpper("didi@hotmail.com")
res3: String = no match
```

在这个模式定义中，UpperCase 后面跟了一个空括号，这个空括号是必须的，否则模式变成匹配和 UpperCase 相等。即使 UpperCase 没有绑定任何变量，但你可以利用前面 [Scala 专题教程 -Case Class 和模式匹配(5): 模式的种类(四)](http://www.imobilebbs.com/wordpress/archives/5041)的方法为这个模式定义绑定一个自定义的变量。这是使用“@”来定义的。本例为x。
