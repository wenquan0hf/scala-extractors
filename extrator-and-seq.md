# Extractors 和 Seq 模式 

我们在前面介绍模式匹配介绍了可以使用如下的方式访问列表的元素：

```
List()
List(x,y,_*)
Array(x,0,0,_)
```

实际上，这些序列模式内部实现都是使用 Extractor 来定义的。 比如 Scala 标准库的 List 的定义具有下面的定义：

```
package scala{
	def apply[T](elems: T*) = elems.toList
	def unapplySeq[T](x:List[T]): Option[Seq[T]] =Some(x)
	...
}
```
