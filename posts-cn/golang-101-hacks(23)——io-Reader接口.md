注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译

`io.Reader` 是一个基础性的 且使用非常频繁的接口

```
type Reader interface {
        Read(p []byte) (n int, err error)
}

```
对于实现了`io.Readerr`的接口类型，可以将它想象成一个管道。将内容写入管道的一端，通过实现了' Read() '方法的类型从管道的另一端读取内容。无论是普通文件，还是网络套接字。只要实现了`io.Readerr`的接口的类型，都可以读取其内容。
如下例所示
```
package main

import (
    "fmt"
    "io"
    "log"
    "os"
)

func main() {
    file, err := os.Open("test.txt")
    if err != nil {
        log.Fatal(err)
    }
    defer file.Close()

    p := make([]byte, 4)
    for {
        if n, err := file.Read(p); n > 0 {
            fmt.Printf("Read %s\n", p[:n])
        } else if err != nil {
            if err == io.EOF {
                fmt.Println("Read all of the content.")
                break
            } else {
                log.Fatal(err)
            }
        } else /* n == 0 && err == nil */ {
            /* do nothing */
        }
    }
}

```
在执行' read() '，有3种情况需要考虑:
You can see after issuing a `read()` call, there are `3` scenarios need to be considered:
(1) `n > 0 `:读取有效内容;继续执行;
(2) ' n == 0 && err != nil ':如果' err '是' `io.EOF`，表示是所有的内容读取完毕，如果' err '是不是' `io.EOF`代表着其他程序异常，需要进行特殊操作;
(3) ' n == 0 && err == nil ':根据[io package document](https://golang.org/pkg/io/#Reader)，意味着什么都没有发生，所以也不需要做任何事情。

创建文件名`test.txt`,其内容只包含5个字节
```
# cat test.txt
abcde

```
执行上面程序，运行结果如下
```
Read abcd
Read e
Read all of the content.

```
参考:
[io package document](https://golang.org/pkg/io/#Reader).
