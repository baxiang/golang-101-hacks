注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
往切片中增加数时，如果切片的所关联的数组没有足够的空间，会重新开辟一个新的数组空间。同时将原先数组中的元素复制到这个新数组对应的内存中，将新添加数据加到数组尾部。因此，在使用Go内置的append函数时，需要小心谨慎，始终牢记“数组可能已经更改”的思想!
下面刻意营造的的例子来说明我们需要注意的点：
```
package main

import (
    "fmt"
)

func addTail(s []int)  {
    var ns [][]int
    for _, v := range []int{1, 2} {
        ns = append(ns, append(s, v))
    }
    fmt.Println(ns)
}

func main() {
    s1 := []int{0, 0}
    s2 := append(s1, 0)
    for _, v := range [][]int{s1, s2} {
        addTail(v)
    }
}   
```
s1是[0,0] s2是[0,0,0],执行addTail函数分别将1和2加到切片的尾部，我们期望的结果是：
```
[[0 0 1] [0 0 2]]
[[0 0 0 1] [0 0 0 2]]
```
但是实际的输出结果是：
```
[[0 0 1] [0 0 2]]
[[0 0 0 2] [0 0 0 2]]
```
运行结果s1的与期望结果一致，但是s2却不是
让我们用delve调试这个问题，检查slice的内部机制 addTail函数设置断点，查看s1时第一执行:
```
(dlv) n
> main.addTail() ./slice.go:8 (PC: 0x401022)
     3: import (
     4:         "fmt"
     5: )
     6:
     7: func addTail(s []int)  {
=>   8:         var ns [][]int
     9:         for _, v := range []int{1, 2} {
    10:                 ns = append(ns, append(s, v))
    11:         }
    12:         fmt.Println(ns)
    13: }
(dlv) p s
[]int len: 2, cap: 2, [0,0]
(dlv) p &s[0]
(*int)(0xc82000a2a0)
```
s1的长度和容量都是2，执行的底层数组地址是0xc82000a2a0，当执行ns = append(ns, append(s, v))时由于s1的长度和容量都是2，已经没有空间给存储新的值了。要增加一个新值，必须创建一个新数组，它包含s1中的[0,0]和新值(1或2)。执行“ns = append(ns, append(s, v))”后发现:
```
(dlv) n
> main.addTail() ./slice.go:9 (PC: 0x401217)
     4:         "fmt"
     5: )
     6:
     7: func addTail(s []int)  {
     8:         var ns [][]int
=>   9:         for _, v := range []int{1, 2} {
    10:                 ns = append(ns, append(s, v))
    11:         }
    12:         fmt.Println(ns)
    13: }
    14:
(dlv) p ns
[][]int len: 1, cap: 1, [
        [0,0,1],
]
(dlv) p ns[0]
[]int len: 3, cap: 4, [0,0,1]
(dlv) p &ns[0][0]
(*int)(0xc82000e240)
(dlv) p s
[]int len: 2, cap: 2, [0,0]
(dlv) p &s[0]
(*int)(0xc82000a2a0)
```
可以看到，新切片的长度为3，容量为4，底层数组地址为0xc82000e240，与s1 (0xc82000a2a0)不同。继续执行，直到退出循环:
We can see the length of anonymous slice is 3, capacity is 4, and the underlying array address is 0xc82000e240, different from s1's (0xc82000a2a0). Continue executing until exit loop:
```
(dlv) n
> main.addTail() ./slice.go:12 (PC: 0x40124c)
     7: func addTail(s []int)  {
     8:         var ns [][]int
     9:         for _, v := range []int{1, 2} {
    10:                 ns = append(ns, append(s, v))
    11:         }
=>  12:         fmt.Println(ns)
    13: }
    14:
    15: func main() {
    16:         s1 := []int{0, 0}
    17:         s2 := append(s1, 0)
(dlv) p ns
[][]int len: 2, cap: 2, [
        [0,0,1],
        [0,0,2],
]
(dlv) p &ns[0][0]
(*int)(0xc82000e240)
(dlv) p &ns[1][0]
(*int)(0xc82000e280)
(dlv) p &s[0]
(*int)(0xc82000a2a0)
```
我们可以看到s1 n[0] n[1]分别指向有3个不同的数组。
现在，让我们按照相同的步骤查看s2 看看到底发生了什么:
```
(dlv) n
> main.addTail() ./slice.go:8 (PC: 0x401022)
     3: import (
     4:         "fmt"
     5: )
     6:
     7: func addTail(s []int)  {
=>   8:         var ns [][]int
     9:         for _, v := range []int{1, 2} {
    10:                 ns = append(ns, append(s, v))
    11:         }
    12:         fmt.Println(ns)
    13: }
(dlv) p s
[]int len: 3, cap: 4, [0,0,0]
(dlv) p &s[0]
(*int)(0xc82000e220)
```
s2的长度是3，容量是4，所以有一个空间可以添加新元素。第一次执行“ns = append(ns, append(s, v))”后查看s2和ns的值:
```
(dlv)
> main.addTail() ./slice.go:9 (PC: 0x401217)
     4:         "fmt"
     5: )
     6:
     7: func addTail(s []int)  {
     8:         var ns [][]int
=>   9:         for _, v := range []int{1, 2} {
    10:                 ns = append(ns, append(s, v))
    11:         }
    12:         fmt.Println(ns)
    13: }
    14:
(dlv) p ns
[][]int len: 1, cap: 1, [
        [0,0,0,1],
]
(dlv) p &ns[0][0]
(*int)(0xc82000e220)
(dlv) p s
[]int len: 3, cap: 4, [0,0,0]
(dlv) p &s[0]
(*int)(0xc82000e220)
```
我们可以看到新切片的数组地址也是0xc82000e220，这是因为s2有足够的空间容纳新元素，不需要分配新的数组。添加2后再次查看s2和ns:
```
(dlv)
> main.addTail() ./slice.go:12 (PC: 0x40124c)
     7: func addTail(s []int)  {
     8:         var ns [][]int
     9:         for _, v := range []int{1, 2} {
    10:                 ns = append(ns, append(s, v))
    11:         }
=>  12:         fmt.Println(ns)
    13: }
    14:
    15: func main() {
    16:         s1 := []int{0, 0}
    17:         s2 := append(s1, 0)
(dlv) p ns
[][]int len: 2, cap: 2, [
        [0,0,0,2],
        [0,0,0,2],
]
(dlv) p &ns[0][0]
(*int)(0xc82000e220)
(dlv) p &ns[1][0]
(*int)(0xc82000e220)
(dlv) p s
[]int len: 3, cap: 4, [0,0,0]
(dlv) p &s[0]
(*int)(0xc82000e220)
```
3个切片都指向同一个数组，因此后面的值(2)将覆盖前面的项(1)。
总之，append函数处理起来非常棘手，因为它可以在您毫不知情下修改底层数组。必须清楚地了解每个切片底层数组的内存分配，否则切片可能会给您带来一个大大的surprise!
