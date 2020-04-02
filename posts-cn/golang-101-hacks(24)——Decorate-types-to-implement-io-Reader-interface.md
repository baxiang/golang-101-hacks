注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译

[io包](https://golang.org/pkg/io/)提供了一组便捷的读取函数和方法，但同时都需要参数满足[io.Reader](https://golang.org/pkg/io/#Reader)接口。请看下面的例子:
```
package main

import (
    "fmt"
    "io"
)

func main() {
    s := "Hello, world!"
    p := make([]byte, len(s))
    if _, err := io.ReadFull(s, p); err != nil {
        fmt.Println(err)
    } else {
        fmt.Printf("%s\n", p)
    }
} 

```
编译程序，会发生如下错误:
Compile above program and an error is generated:

```
read.go:11: cannot use s (type string) as type io.Reader in argument to io.ReadFull:
    string does not implement io.Reader (missing Read method)

```
[io.ReadFull](https://golang.org/pkg/io/#ReadFull)函数要求参数应该实现了' io.Reader '，但' string '类型没有`Read() `方法，所以需要对' s '变量做转换。将`io.ReadFull(s, p)`转换为`io.ReadFull(strings.NewReader(s), p)`：
```
package main

import (
    "fmt"
    "io"
    "strings"
)

func main() {
    s := "Hello, world!"
    p := make([]byte, len(s))
    if _, err := io.ReadFull(strings.NewReader(s), p); err != nil {
        fmt.Println(err)
    } else {
        fmt.Printf("%s\n", p)
    }
}

```
这次编译成功 运行结果如下
```
Hello, world!

```
[strings.NewReader](https://golang.org/pkg/strings/#NewReader)函数可以将字符串转换成[strings.Reader](https://golang.org/pkg/bytes/#Reader)

```
func (r *Reader) Read(b []byte) (n int, err error)  

```

Besides `string`, another common operation is to use [bytes.NewReader](https://golang.org/pkg/bytes/#NewReader) to convert a byte slice into a [bytes.Reader](https://golang.org/pkg/bytes/#Reader) struct which satisfies `io.Reader` interface. Do some modifications on the above example:

```
 package main

import (
    "bytes"
    "fmt"
    "io"
    "strings"
)

func main() {
    s := "Hello, world!"
    p := make([]byte, len(s))
    if _, err := io.ReadFull(strings.NewReader(s), p); err != nil {
        fmt.Println(err)
    }

    r := bytes.NewReader(p)
    if b, err := r.ReadByte(); err != nil {
        fmt.Println(err)
    } else {
        fmt.Printf("%c\n", b)
    }
}

```
`bytes.NewReader` 将`p`切片转换为`bytes.Reader`结构。输出结果如下:
```
H
```
