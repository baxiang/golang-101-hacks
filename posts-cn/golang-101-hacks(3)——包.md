注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译,[原文地址]([https://nanxiao.gitbooks.io/golang-101-hacks/content/posts/package.html](https://nanxiao.gitbooks.io/golang-101-hacks/content/posts/package.html)
)
在“Go”中，包分为两种类型:
(1) main包:用于生成可执行的二进制文件，main函数是程序的入口点。下面以helllo.go 为例:

```
package main

import "greet"

func main() {
	greet.Greet()
}

```
(2)其他类型的包也可以在细分成两类:
库文件包:用来生成可以被其他人重用的目标文件。如greet.go这个文件

```
package greet

import "fmt"

func Greet() {
	fmt.Println("Hello 中国!")
}

```

b)另外的包主要是特殊用途的，比如测试。
当程序需要引用“Go”标准包(“$GOROOT”)或第三方包(“$GOPATH”)时，在顶部声明“import”:

```
import "fmt"
import "github.com/NanXiao/stack" 

```

Or:

```
import (
	"fmt"
	"github.com/NanXiao/stack"
)

```
在上面的例子中，为了导入相关的类包 需要声明'fm和github.com/NanXiao/stack的包路径
In the above examples, the "`fmt`" and "`github.com/NanXiao/stack`" are called `import path`, which is used to find the relevant package.
你也可以看到如下的用法
You may also see the following cases:

```
import m "lib/math" // 使用m作为math包的别名
import . "lib/math" // 当使用点号时math包时可以省略包名
```
如果执行go install命令找不到指定的包，它会报告如下错误消息
If the `go install` command can't find the specified package, it will complain the error messages like this:

```
... : cannot find package "xxxx" in any of:
        /usr/local/go/src/xxxx (from $GOROOT)
        /root/gowork/src/xxxx (from $GOPATH)

```
为了避免包引用出现冲突，需要确保包的路径地址的唯一性，例如以github仓库地址作为标识
To avoid library conflicts, you'd better make your own packages' path the only one in the world: E.g., your `github`repository destination:

```
 github.com/NanXiao/...

```
尽管不是强制要求，通常良好的编程习惯是包名称使用包路径地址最后的结尾项名称。
**Conventionally**, your package name should be same with the last item in `import path`; it is a good coding habit though not a must.

Reference:
[The Go Programming Language](http://www.gopl.io/).
