注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译

与[io.Reader](https://golang.org/pkg/io/#Reader) 接口相对应的就是 [io.Writer](https://golang.org/pkg/io/#Writer)接口了

```
type Writer interface {
        Write(p []byte) (n int, err error)
}

```
与`io.Reader`相比，不需要考虑`io.EOF` 错误， `Write`方法很简单:

Compared to `io.Reader`, since you no need to consider `io.EOF` error, the process of `Write`method is simple:
当`err == nil` 表示所有数据写入成功
(1) `err == nil`: All the data in `p` is written successfully;
(2) ' err != nil ': 表示`p` 中的数据部分或都没有写入成功。
(2) `err != nil`: The data in `p` is partially or not written at all.
查看下面的例子
Let's see an example:
```
package main

import (
        "log"
        "os"
)

func main() {
        f, err := os.Create("test.txt")
        if err != nil {
                log.Fatal(err)
        }
        defer f.Close()

        if _, err = f.Write([]byte{'H', 'E', 'L', 'L', 'O'}); err != nil {
                log.Fatal(err)
        }
}

```
执行程序，`test.txt` 被创建
After executing the program, the `test.txt` is created:

```
# cat test.txt
HELLO
```
