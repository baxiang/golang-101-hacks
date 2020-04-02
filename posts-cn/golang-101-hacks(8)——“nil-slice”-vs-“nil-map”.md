注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
在GO语言中Slice和map都是引用类型，默认值都是`nil`
```
package main

import "fmt"

func main() {
    var (
        s []int
        m map[int]bool
    )
    if s == nil {
        fmt.Println("The value of s is nil")
    }
    if m == nil {
        fmt.Println("The value of m is nil")
    }
}  

```
执行结果是：
```
The value of s is nil
The value of m is nil

```
当一个切片的值为“nil”时，是可以对它进行添加数据等操作。
```
package main

import "fmt"

func main() {
    var s []int
    fmt.Println("Is s a nil? ", s == nil)
    s = append(s, 1)
    fmt.Println("Is s a nil? ", s == nil)
    fmt.Println(s)
}

```
执行结果是：
The result is like this：

```
Is s a nil?  true
Is s a nil?  false
[1]

```
需要注意的是当slice是nil和没有值两种状况的slice长度都是0
A caveat you should notice is the length of both `nil` and empty slice is `0`:

```
package main

import "fmt"

func main() {
    var s1 []int
    s2 := []int{}
    fmt.Println("Is s1 a nil? ", s1 == nil)
    fmt.Println("Length of s1 is: ", len(s1))
    fmt.Println("Is s2 a nil? ", s2 == nil)
    fmt.Println("Length of s2 is: ", len(s2))
}

```
运行结果如下
The result is like this：
```
Is s1 a nil?  true
Length of s1 is:  0
Is s2 a nil?  false
Length of s2 is:  0

```
将slice的值与' nil '进行比较，以检查它是否是' nil '。
对一个“nil”的map取值是没有问题的，但是存储“nil”的map会引起程序panic:
```
package main

import "fmt"

func main() {
    var m map[int]bool
    fmt.Println("Is m a nil? ", m == nil)
    fmt.Println("m[1] is ", m[1])
    m[1] = true
}

```
运行结果如下
```
Is m a nil?  true
m[1] is  false
panic: assignment to entry in nil map

goroutine 1 [running]:
panic(0x4cc0e0, 0xc082034210)
    C:/Go/src/runtime/panic.go:481 +0x3f4
main.main()
    C:/Work/gocode/src/Hello.go:9 +0x2ee
exit status 2

Process finished with exit code 1

```
因此对map进行数据操作是 需要提前进行初始化，如下所示

```
m := make(map[int]bool)

```
顺便补充一点，建议使用下面的代码方式来判断map中是否存在某个键值:

```
if v, ok := m[1]; !ok {
    .....
}

```

参考
[The Go Programming Language](http://www.gopl.io/).
