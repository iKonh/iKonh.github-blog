---
title: 初识Go--init与main方法
date: 2018-06-27 10:18:31
tags: Go
---

初次接触Go，不用说，肯定要从通过入口方法输出“HelloWord”开始，优先保证环境正常。但是Go得HelloWord并没有那么容易打出来，Go对入口方法做了一些限制，所以就引出了需要对入口方法main以及初始化方法init做一些总结。
## main方法
main方法必须定义在main包中，并且只能有一个，否则无法识别main方法。

## init方法
init方法没有包的限制，在一个类中可以同时存在多个init方法。

## init和main的优先级
这里对于init方法及main方法一些优先级的限制：<br/>

对同一个go文件的init()调用顺序是从上到下的。<br/>

对同一个package中不同文件是按文件名字符串比较“从小到大”顺序调用各文件中的init()函数。<br/>

对于不同的package，如果不相互依赖的话，按照main包中"先import的后调用"的顺序调用其包中的init()
如果package存在依赖，则先调用最早被依赖的package中的init()。<br/>

最后调用main方法。

一个简单的例子如下：

```golang
package main
import (
	"fmt"
)
func main() {
	fmt.Print("main")
}
func init()  {
	fmt.Println("init1")
}
func init() {
	fmt.Println("init2")
}
```
	
运行后输出结果如下：

```
init1
init2
main
```