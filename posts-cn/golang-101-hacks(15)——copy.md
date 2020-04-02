注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
内置函数[copy](https://golang.org/pkg/builtin/#copy)定义如下：
>func copy(dst, src []Type) int
内置函数copy将元素从源切片复制到目标切片。（作为一种特殊情况，它还会将字符串中的字节复制到一个字节片段）切片源和目标可能存在重叠。Copy返回值是copy的总数，即是len（src）和len（dst）的最小值。

看一下基础演示代码，其源切片和目标切片双方不存在重叠

```
package main

import (
    "fmt"
)

func main() {
    d := make([]int, 3, 5)
    s := []int{2, 2}
    fmt.Println("Before copying (destination slice): ", d)
    fmt.Println("Copy length is: ", copy(d, s))
    fmt.Println("After copying (destination slice): ", d)

    d = make([]int, 3, 5)
    s = []int{2, 2, 2}
    fmt.Println("Before copying (destination slice): ", d)
    fmt.Println("Copy length is: ", copy(d, s))
    fmt.Println("After copying (destination slice): ", d)

    d = make([]int, 3, 5)
    s = []int{2, 2, 2, 2}
    fmt.Println("Before copying (destination slice): ", d)
    fmt.Println("Copy length is: ", copy(d, s))
    fmt.Println("After copying (destination slice): ", d)

}

```
上面的代码中，目标切片长度是3，源切片长度分别是2，3，4，查看运行结果：
```
Before copying (destination slice):  [0 0 0]
Copy length is:  2
After copying (destination slice):  [2 2 0]
Before copying (destination slice):  [0 0 0]
Copy length is:  3
After copying (destination slice):  [2 2 2]
Before copying (destination slice):  [0 0 0]
Copy length is:  3
After copying (destination slice):  [2 2 2]

```
由此可见复制元素的最终个数是源切片和目标切片两者间的最小长度。

We can make sure the number of copied elements is indeed the minimum length of source and destination slices.
接着看存在重叠元素的复制演示程序：

```
package main

import (
    "fmt"
)

func main() {
    d := []int{1, 2, 3}
    s := d[1:]

    fmt.Println("Before copying: ", "source is: ", s, "destination is: ", d)
    fmt.Println(copy(d, s))
    fmt.Println("After copying: ", "source is: ", s, "destination is: ", d)

    s = []int{1, 2, 3}
    d = s[1:]

    fmt.Println("Before copying: ", "source is: ", s, "destination is: ", d)
    fmt.Println(copy(d, s))
    fmt.Println("After copying: ", "source is: ", s, "destination is: ", d)
}

```
结果如下：
```
Before copying:  source is:  [2 3] destination is:  [1 2 3]
2
After copying:  source is:  [3 3] destination is:  [2 3 3]
Before copying:  source is:  [1 2 3] destination is:  [2 3]
2
After copying:  source is:  [1 1 2] destination is:  [1 2]

```
通过输出结果可以看出，无论源切片是否在目标切片之前，结果总是与预期一致。因此可以人为:源切片中的数据复制到一个临时切片，然后将元素从临时切片复制到目标切片。
Through the output, we can see no matter the source slice is ahead of destination or not, the result is always as expected. You can think the implementation is like this: the data from source slice are copied to a temporary place first, then the elements are copied from temporary to destination slice.

“copy”要求源切片和目标切片的数据类型需要一致性，但存在例外是源切片是字符串，而目标片是“[]byte”:

```
package main

import (
    "fmt"
)

func main() {
    d := make([]byte, 20, 30)
    fmt.Println(copy(d, "Hello, 中国"))
    fmt.Println(string(d))
} 

```

输出结果如下
```
13
Hello, 中国
```

参考:
[copy() behavior when overlapping](https://groups.google.com/forum/#!msg/Golang-Nuts/HI6RI18S8L0/v6xevVPeS9EJ).
