注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
在Slice中添加元素:可以使用“Go”内置函数[append](https://golang.org/pkg/builtin/#append)
`Go` has a built-in [append](https://golang.org/pkg/builtin/#append) function which add elements in the slice:
```
func append(slice []Type, elems ...Type) []Type
```
但是如果需要增加到最前面，可以使用copy函数，如下
But how if we want to the "prepend" effect? Maybe we should use `copy` function. E.g.:

```
package main

import "fmt"

func main()  {
    var s []int = []int{1, 2}
    fmt.Println(s)

    s1 := make([]int, len(s) + 1)
    s1[0] = 0
    copy(s1[1:], s)
    s = s1
    fmt.Println(s)

} 

```
运行结果
The result is like this:
```
[1 2]
[0 1 2]

```
上面的代码看上去不是很完美，一种友好的写法如下
But the above code looks ugly and cumbersome, so an elegant implementation maybe here:

```
s = append([]int{0}, s...)

```
顺便补充一点，我尝试写了一个通用的前置增加函数
BTW, I also have tried to write a "general-purpose" prepend:

```
func Prepend(v interface{}, slice []interface{}) []interface{}{
    return append([]interface{}{v}, slice...)
}

```
但是由于`[]T `不能直接转换成`[]interface{} `(请参考[https://golang.org/doc/faq#convert_slice_of_interface](https://golang.org/doc/faq#convert_slice_of_interface)，因此也就是玩玩啦，没什么实际意义。
But since `[]T` can't convert to an `[]interface{}` directly (please refer [https://golang.org/doc/faq#convert_slice_of_interface](https://golang.org/doc/faq#convert_slice_of_interface), it is just a toy, not useful.

参考：:
[Go – append/prepend item into slice](https://codingair.wordpress.com/2014/07/18/go-appendprepend-item-into-slice/).
