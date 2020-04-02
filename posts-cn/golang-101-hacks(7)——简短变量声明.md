注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
在GO语言中简短变量声明是一种非常方便的“声明变量”方式 
short variable declaration is a very convenient manner of "declaring variable" in `Go`:

```
i := 10

```
以下是简写方式（注意变量没有标明类型）：
It is shorthand of following (Please notice there is no type):

```
var i = 10

```
Go编译器会根据变量的值来推断变量的类型。这将带来极大方便，但另一方面，也带来了一些陷阱，应该注意:
The `Go` compiler will infer the type according to the value of variable. It is a very handy feature, but on the other side of coin, it also brings some pitfalls which you should pay attention to:
(1)简短变量声明格式只能用于函数体内
(1) This format can only be used in functions:

```
package main

i := 10

func main() {
    fmt.Println(i)
}

```
编译体会提示如下错误

```
syntax error: non-declaration statement outside function body

```
(2)必须声明**至少一个新变量**:
```
package main

import "fmt"

func main() {
    var i = 1

    i, err := 2, true

    fmt.Println(i, err)
}

```
在`i, err:= 2, false `语句中，只有' err '是一个新声明的变量，`var`实际上是在 `vari = 1`中声明的。
简短变量声明可以隐藏全局变量声明，它可能不是您想要的，这让您大吃一惊:
```
package main

import "fmt"

var i = 1

func main() {

    i, err := 2, true

    fmt.Println(i, err)
}

```
' i, err:= 2, true '实际上声明了一个**new local i**，这使得**global i**在' main '函数中没有被访问。使用全局变量，不使用局部变量，正确的解决方法如下:

```
package main

import "fmt"

var i int

func main() {

    var err bool

    i, err = 2, true

    fmt.Println(i, err)
}

```

参考：
[Short variable declarations](https://golang.org/ref/spec#Short_variable_declarations).
