注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
在Go中，数组是相同数据类型组成长度固定的连续内存数据结构，slice只是指向底层数组的引用类型。它们是不同的类型，因此不能彼此直接赋值。请看下面的例子:
```
package main

import "fmt"

func main() {
    s := []int{1, 2, 3}
    var a [3]int

    fmt.Println(copy(a, s))
}

```
copy函数只能接收切片参数，我们可以使用`[:]`方式将数组转换成切片，看下面的代码
```
package main

import "fmt"

func main() {
    s := []int{1, 2, 3}
    var a [3]int

    fmt.Println(copy(a[:2], s))
    fmt.Println(a)
}

```
运行结果是：

```
2
[1 2 0]

```
上面的例子是将值从切片复制到数组，相反的从数组到切片也是异曲同工:
```
package main

import "fmt"

func main() {
    a := [...]int{1, 2, 3}
    s := make([]int, 3)

    fmt.Println(copy(s, a[:2]))
    fmt.Println(s)
}

```
执行结果如下
```
2
[1 2 0]

```
参考
[In golang how do you convert a slice into an array](http://stackoverflow.com/questions/19073769/in-golang-how-do-you-convert-a-slice-into-an-array);
[Arrays, slices (and strings): The mechanics of 'append'](https://blog.golang.org/slices).
