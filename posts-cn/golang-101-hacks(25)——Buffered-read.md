注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译

[bufio](https://golang.org/pkg/bufio/)包提供缓冲读取的函数。如下面例子所示

(1) 首先创建`test.txt`
```
# cat test.txt
abcd
efg
hijk
lmn

```
 `test.txt` 文件包含4行内容

(2) 接着看下面程序:

```
package main

import (
        "bufio"
        "fmt"
        "io"
        "log"
        "os"
)

func main() {
        f, err := os.Open("test.txt")
        if err != nil {
                log.Fatal(err)
        }

        r := bufio.NewReader(f)
        for {
                if s, err := r.ReadSlice('\n'); err == nil || err == io.EOF {
                        fmt.Printf("%s", s)
                        if err == io.EOF {
                                break
                        }
                } else {
                        log.Fatal(err)
                }

        }
}

```

(a)打开test.txt文件

```
f, err := os.Open("test.txt")

```


(b)

```
r := bufio.NewReader(f)

```
`bufio.NewReader(f)`生成一个 [bufio.Reader](https://golang.org/pkg/bufio/#Reader)结构体，其实现了缓冲读取的方法
(c)

```
for {
    if s, err := r.ReadSlice('\n'); err == nil || err == io.EOF {
        fmt.Printf("%s", s)
        if err == io.EOF {
            break
        }
    } else {
        log.Fatal(err)
    }

}

```
读取并打印每一行
运行结果如下
```
abcd
efg
hijk
lmn

```
可以使用[bufio.Scanner](https://golang.org/pkg/bufio/#Scanner)来实现"打印每行"功能:
```
package main

import (
        "bufio"
        "fmt"
        "log"
        "os"
)

func main() {
        f, err := os.Open("test.txt")
        if err != nil {
                log.Fatal(err)
        }

        s := bufio.NewScanner(f)

        for s.Scan() {
                fmt.Println(s.Text())
        }
}

```

(a)

```
s := bufio.NewScanner(f)

```
`bufio.NewScanner(f)`创建一个[bufio.Scanner](https://golang.org/pkg/bufio/#Scanner)结构体，默认情况下按行分割内容。

`bufio.NewScanner(f)` creates a new [bufio.Scanner](https://golang.org/pkg/bufio/#Scanner) struct which splits the content by line by default.

(b)

```
for s.Scan() {
    fmt.Println(s.Text())
}

```
`s.Scan()`前进`bufio.Scanner`到下一个标记（在这种情况下，它是一个可选的回车后跟一个强制换行），我们可以使用`s.Text()`函数来获取内容。

我们还可以自定义[SplitFunc](https://golang.org/pkg/bufio/#SplitFunc)函数，它不会按行分隔。查看下面代码：



```
package main

import (
        "bufio"
        "fmt"
        "log"
        "os"
)

func main() {
        f, err := os.Open("test.txt")
        if err != nil {
                log.Fatal(err)
        }

        s := bufio.NewScanner(f)
        split := func(data []byte, atEOF bool) (advance int, token []byte, err error) {
                for i := 0; i < len(data); i++ {
                        if data[i] == 'h' {
                                return i + 1, data[:i], nil
                        }
                }

                return 0, data, bufio.ErrFinalToken
        }
        s.Split(split)
        for s.Scan() {
                fmt.Println(s.Text())
        }
}

```

split函数用“ h” 分隔内容，运行结果为

```
abcd
efg

ijk
lmn
```
