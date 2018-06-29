---
title: 初识Go--iota的用法与枚举
date: 2018-06-29 11:22:44
tags: Go
---

其实这篇文章的内容都是我在看常量相关的介绍时总结的，只总结这些是因为其他的比较好理解，而这两点需要实际的代码操作才能理解的原因。
##iota
当我们在做一个系统对接时，难免会遇到定义方法返回码的时候。我们需要定义 成功的返回码是多少，失败原因1的返回码是多少，失败原因2的返回码是多少。如果返回码多了，很有可能遇到返回码冲突的情况，造成上游系统的判断bug。<br/>

而在Go中，这个问题就很好解决了，我们可以借助iota来搞定。<br/>

iota:Go提供的一个系统常量，数值类型，第一次使用默认为0，每使用一次默认+1。<br/>

有了iota，我们就可以用下列方式来定义返回码：

```golang
const (
	SUCCESS = iota
	FAILED_QUERY
	FAILED_INSERT
	FAILED_UPDATE
)

func init() {
	fmt.Println(SUCCESS, FAILED_QUERY, FAILED_INSERT, FAILED_UPDATE)
}
```

输出结果：
```
0 1 2 3
```

关于iota还有几点需要注意下：

1.iota只能用于常量的声明，如果用于变量则会报错

2.iota只有用于同一常量块中，才会有自动+1的操作，否则每次使用都会自动初始化为0<br/>
例：

```golang
const (
	SUCCESS = iota
	FAILED_QUERY
	FAILED_INSERT
	FAILED_UPDATE
)
const a = iota
const b = iota

func init() {
	fmt.Println(SUCCESS, FAILED_QUERY, FAILED_INSERT, FAILED_UPDATE, a, b)
}
```

输出结果：0 1 2 3 0 0

3.同一常量块中，即使接下来的常量是其他类型的值，也不妨碍iota的计算<br/>
例:

```golang
const (
	SUCCESS = iota
	FAILED = "123"
	FAILED_QUERY =iota
	FAILED_INSERT
	FAILED_UPDATE
)

func init() {
	fmt.Println(SUCCESS, FAILED_QUERY, FAILED_INSERT, FAILED_UPDATE)
}
```
结果：0 2 3 4

##枚举
Go中本身是没有枚举的，需要自定义。定义枚举的时候，就非常需要结合iota
如下，定义一个星期的枚举：

```golang
type WEEK int

const (
	MON WEEK = iota + 1
	TUE
	WEN
	THE
	FRI
	SAT
	SUN
)

func main() {
	var a = SUN
	fmt.Println(reflect.TypeOf(a), a)
}
```
输出结果：main.WEEK 7

由于枚举并不是Go本身的语法，而是自己定义的，所以并不是很严格，例如：

```golang
const (
	MON WEEK = iota + 1
	TUE
	WEN
	THE
	FRI
	SAT
	SUN
)

func main() {
	var a = SUN // 此处给a定义为WEEK类型
	a = 15 // 此处重新给a赋值15
	fmt.Println(reflect.TypeOf(a), a)
}
```
输出结果：main.WEEK 15<br/>
可见a变量其实还是WEEK类型,因为WEEK类型并不是一个独立类型，而是针对int类型的一个别名，具体可以看一下type的用法，这里不再说明。
