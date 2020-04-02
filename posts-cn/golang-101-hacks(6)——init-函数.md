注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
顾名思义init函数就是开展一些初始化任务，比如初始化变量值，或程序状态。一个源文件可以包含一个或多个init()函数，如下所示：
```
package main

import "fmt"

var global int = 0

func init() {
    global++
    fmt.Println("In first Init(), global is: ", global)
}

func init() {
    global++
    fmt.Println("In Second Init(), global is: ", global)
}

func main() {
    fmt.Println("In main(), global is: ", global)
}

```
执行结果如下：
```
In first Init(), global is:  1
In Second Init(), global is:  2
In main(), global is:  2

```
一个包可以包含多个源文件，也就可能存在多个init()函数。这将会导致无法确定哪个源文件的' init() '函数先被执行。唯一可以确定的是，包中声明的变量将会在' init() '函数之前被初始化。

看另外一个演示程序， `$GOROOT/src`的目录如下


```
# tree
.
├── foo
│   └── foo.go
└── play
    └── main.go

```
存在2个包文件：`foo` 和 `play`，foo包的foo.go内容如下

```
package foo

import "fmt"

var Global int

func init() {
        Global++
        fmt.Println("foo init() is called, Global is: ", Global)
}

```
play包中main.go内容如下

```
package main

import "foo"

func main() {
}

```
编译主函数play

```
# go install play
# play
src/play/main.go:3: imported and not used: "foo"
```
导致编译失败的原因是main.go 未使用foo包的任何函数和变量值，如果只是调用init函数 ，你可以修改成`import _ "foo"`"
```
 package main

import _ "foo"

func main() {
}

```
现在程序编译成功，程序的输出结果是：

```
# play
foo init() is called, Global is:  1

```

参考
[Effective Go](https://golang.org/doc/effective_go.html#init);
[When is the init() function in go (golang) run?](http://stackoverflow.com/questions/24790175/when-is-the-init-function-in-go-golang-run)

