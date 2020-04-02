注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译
接口是由若干方法组成的引用类型，包含了接口的所有方法的类型被认为自动实现了该接口类。通过接口，您可以更加体会到面向对象编程。如下所示:
```
package main

import "fmt"

type Foo interface {
    foo()
}

type A struct {
}

func (a A) foo() {
    fmt.Println("A foo")
}

func (a A) bar() {
    fmt.Println("A bar")
}

func callFoo(f Foo) {
    f.foo()
}

func main() {
    var a A
    callFoo(a)
}

```
运行结果如下
```
A foo

```
让我们具体分析一下代码
```
type Foo interface {
    foo()
}

```
上面的接口`Foo`只有一个方式`foo()`.
```
type A struct {
}

func (a A) foo() {
    fmt.Println("A foo")
}

func (a A) bar() {
    fmt.Println("A bar")
}

```
struct ' A '有 2 方法:`foo() `和`bar() `。因为它已经实现了' foo() '方法，所以它满足' foo '接口。

```
func callFoo(f Foo) {
    f.foo()
}

func main() {
    var a A
    callFoo(a)
}

```
 callFoo '需要一个类型为' Foo '接口的变量，传递' a '是可以的。' callFoo '将使用' A 's ' foo() '方法，并打印' A foo '。

修改main函数
```
func main() {
    var a A
    callFoo(&a)
}

```
这次，' callFoo() '的参数是' &a '，其类型是指针类型A 。编译并运行程序，会发现同样输出了:" ' A foo ' "。指针类型A 类型拥有引用类型' A '拥有的所有方法。但事实并非如此:
```
package main

import "fmt"

type Foo interface {
    foo()
}

type A struct {
}

func (a *A) foo() {
    fmt.Println("A foo")
}

func (a *A) bar() {
    fmt.Println("A bar")
}

func callFoo(f Foo) {
    f.foo()
}

func main() {
    var a A
    callFoo(a)
}

```
编译程序

```
example.go:26: cannot use a (type A) as type Foo in argument to callFoo:
A does not implement Foo (foo method has pointer receiver)

```
您还可以看到' *A ' type实现了' foo() '和' bar() '方法，这并不意味着默认情况下' A ' type具有这两个方法。
顺便补充一点，任何类型都实现了空接口:`interface{}`。
接口类型实际上是一个包含2元素的元组:' <type, value> '， ' type '标识存储在接口中变量的类型，而' value '指向实际值。一个接口的默认值是' nil '，这意味着' type '和' value '都是' nil ': ' <nil, nil> '。判断一个接口是否为空时:

```
var err error
if err != nil {
    ...
}  

```
当“type”和“value”都为“nil”时，接口值为“nil”。

参考:
[The Go Programming Language](http://www.gopl.io/).
