# 一. 条件判断if

与其他语言相似，只是表达式不需要用括号包起来，大括号不要换行

```
	//单独使用
	if 表达式 {

	}
```

* 在if语法结构中所有表达式最终结果都是bool类型,true代表成立执行对应代码

# 二. if单独使用

* if单独使用时只影响到自己对应的代码块

```go
func main() {
	score := 65

	if score >= 60 {
		fmt.Println("及格")
	}
}
```

* 可以在if 表达式中声明变量,这个变量的范围只在if中能够使用

```go
func main() {
	if score:=60; score>=60{
		fmt.Println("及格了")
	}
}
```

* 多个单独if使用时相互没有影响

```go
func main() {
	score := 65

	if score >= 60 {
		fmt.Println("及格")
	}
	if score < 60 {
		fmt.Println("不及格")
	}
}
```

# 二. 条件判断switch

* switch每个case分支默认只执行一个且是从上向下执行
* default上下位置没有影响,当且仅当所有case都不成立时才执行default

## switch用法(一)

- 当变量只有固定的几个值时可以使用switch结构

```
func main() {
	num := 16
	switch num {
	case 2:
		fmt.Println("2进制")
	case 8:
		fmt.Println("8进制")
	case 10:
		fmt.Println("10进制")
	case 16:
		fmt.Println("16进制")
	default:
		fmt.Println("内容不正确")
	}
	fmt.Println("程序结束")
}
```

- switch也支持在条件位置定义变量,变量有效范围为当前switch

```
func main() {
	switch num := 16; num {
	case 2:
		fmt.Println("2进制")
	case 8:
		fmt.Println("8进制")
	case 10:
		fmt.Println("10进制")
	case 16:
		fmt.Println("16进制")
	default:
		fmt.Println("内容不正确")
	}
	fmt.Println("程序结束")
}
```

## switch用法(二)

- 当条件是范围而不是固定值时

```
func main() {
	score := 71
	switch {
	case score >= 90:
		fmt.Println("优秀")
	case score >= 80:
		fmt.Println("良好")
	case score >= 70:
		fmt.Println("中等")
	case score >= 60:
		fmt.Println("及格")
	default:
		fmt.Println("不及格")
	}
	fmt.Println("程序结束")
}
```

## switch用法(三)

- case条件支持多个值,每个值使用逗号分开

```
func main() {
	month := 5
	switch month {
	case 1, 3, 5, 7, 8, 10, 12:
		fmt.Println("31天")
	case 2:
		fmt.Println("28或29天")
	default:
		fmt.Println("30天")
	}
	fmt.Println("程序结束")
}
```

## 穿透和中断

- switch结构中某个`最多只能执行一个case`,使用`fallthrough`可以让下一个case/default继续执行

```
func main() {
	switch num := 1; num {
	case 1:
		fmt.Println("1")
		fallthrough
	case 2:
		fmt.Println("2")
	case 3:
		fmt.Println("3")
		fallthrough
	case 4:
		fmt.Println("4")
	default:
		fmt.Println("不是1,2,3,4")
	}
	fmt.Println("程序结束")
}
```

- break可以用在switch和循环中,表示`立即结束`,无论当前结构后面还有多少代码

`break`可以阻止`fallthrough`的执行

```
func main() {
	switch num := 1; num {
	case 1:
		fmt.Println("1")
		break
		fmt.Println("break后面代码都不执行")
		fallthrough
	case 2:
		fmt.Println("2")
	case 3:
		fmt.Println("3")
		fallthrough
	case 4:
		fmt.Println("4")
	default:
		fmt.Println("不是1,2,3,4")
	}
	fmt.Println("程序结束")
}
```

## goto

* goto是Go语言中的一个关键字
* goto让编译器执行时跳转到特定位置
  * Loop是标记名(Label),名称任意,习惯上名称为Loop

```go
	fmt.Println("执行程序")
	i := 6
	if i == 6 {
		goto Loop
	}
	fmt.Println("if下面输出")
Loop:
	fmt.Println("loop")
```

* 可以有多个,但是标签(Labal)定义了就必须使用

```go
	fmt.Println("执行程序")
	i := 6
	if i == 6 {
		goto Loop
	}
	fmt.Println("if下面输出")
Loop:
	fmt.Println("loop")
Loop1: //报错:label Loop1 defined and not used
	fmt.Println("Loop1")
```

* goto也可以用于跳出循环,执行指定标签位置代码

```go
	for i := 0; i < 5; i++ {
		fmt.Println(i)
		if i == 2 {
			goto abc
		}
	}
	fmt.Println("for循环执行结束")
abc:
	fmt.Println("abc")
	fmt.Println("程序结束")
```
# 一. select简介

* Golang中select和switch结构特别像,但是select中case的条件只能是I/O
* select 的语法(condition是条件)

```go
select{
  case condition:
  case condition:
  default:
}
```

* select执行过程:
  * 每个case必须是一个`IO`操作
  * 哪个case可以执行就执行哪个
  * 多个case都可以执行,`随机`执行一个
  * 所有case都不能执行时,执行`default`
  * 所有case都不能执行,且没有default,将会`阻塞`
* 代码示例

```go
func main() {
   runtime.GOMAXPROCS(1)
   ch1 := make(chan int, 1)
   ch2 := make(chan string, 1)
   ch1 <- 1
   ch2 <- "hello"
   select {
   case value := <-ch1:
      fmt.Println(value)
   case value := <-ch2:
      fmt.Println(value)
   }
}
```

* select多和for循环结合使用,下面例子演示出了一直在接收消息的例子

```go
package main

import (
	"fmt"
)

func main() {
	ch := make(chan int)
	for i := 1; i <= 5; i++ {
		go func(arg int) {
			ch <- arg
		}(i)
	}
  //如果是一直接受消息,应该是死循环for{},下面代码中是明确知道消息个数
	for i := 1; i <= 5; i++ {
		select {
		case c := <-ch:
			fmt.Println("取出数据", c)
		default:
			//没有default会出现死锁
		}
	}
	fmt.Println("程序执行结束")
}

```

* `break`可以对select生效,如果for中嵌套select,break选择`最近`结构

