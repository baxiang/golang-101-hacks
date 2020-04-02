注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
切片有3部分组成
a)指针:指向底层数组中首位置;
b)长度(类型为int):切片的有效元素个数;
b)容量(类型为int):切片的容量。
看下面的代码:
```
package main

import (
	"fmt"
	"unsafe"
)

func main() {
	var s1 []int
	fmt.Println(unsafe.Sizeof(s1))
}
```
在64位置系统中打印结果是24（指针和整数类型都是占8个字节）
在下面的栗子中，使用gdb设置断点查看slice结构，代码如下
```
package main

import "fmt"

func main() {
        s1 := make([]int, 3, 5)
        copy(s1, []int{1, 2, 3})
        fmt.Println(len(s1), cap(s1), &s1[0])

        s1 = append(s1, 4)
        fmt.Println(len(s1), cap(s1), &s1[0])

        s2 := s1[1:]
        fmt.Println(len(s2), cap(s2), &s2[0])
}
```
使用gdb断点调试
```
Use gdb to step into the code:

5       func main() {
(gdb) n
6               s1 := make([]int, 3, 5)
(gdb)
7               copy(s1, []int{1, 2, 3})
(gdb)
8               fmt.Println(len(s1), cap(s1), &s1[0])
(gdb)
3 5 0xc820010240
```
在执行“s1 = append(s1, 4)”之前打印输出切片的长度(3)、容量(5)和起始元素地址(0xc820010240)，s1的内存结构:
Before executing "s1 = append(s1, 4)", fmt.Println outputs the length(3), capacity(5) and the starting element address(0xc820010240) of the slice, let's check the memory layout of s1:
```
10              s1 = append(s1, 4)
(gdb) p &s1
$1 = (struct []int *) 0xc82003fe40
(gdb) x/24xb 0xc82003fe40
0xc82003fe40:   0x40    0x02    0x01    0x20    0xc8    0x00    0x00    0x00
0xc82003fe48:   0x03    0x00    0x00    0x00    0x00    0x00    0x00    0x00
0xc82003fe50:   0x05    0x00    0x00    0x00    0x00    0x00    0x00    0x00
(gdb)
```
通过s1的内存地址信息(起始内存地址为0xc82003fe40)，我们可以看到它的内存地址信息与打印的输出结果一致。
Through examining the memory content of s1(the start memory address is 0xc82003fe40), we can see its content matches the output of fmt.Println.
继续往下执行，查看`"s2 := s1[1:]`代码之前的输出结果：
```
(gdb) n
11              fmt.Println(len(s1), cap(s1), &s1[0])
(gdb)
4 5 0xc820010240
13              s2 := s1[1:]
(gdb) x/24xb 0xc82003fe40
0xc82003fe40:   0x40    0x02    0x01    0x20    0xc8    0x00    0x00    0x00
0xc82003fe48:   0x04    0x00    0x00    0x00    0x00    0x00    0x00    0x00
0xc82003fe50:   0x05    0x00    0x00    0x00    0x00    0x00    0x00    0x00
```
往s1添加新元素之后(s1 = append(s1, 4))，s1的长度变成了4，但是容量却还是原值。
We can see after appending a new element(s1 = append(s1, 4)), the length of s1 is changed to 4, but the capacity remains the original value.
查看s2的结构
```
(gdb) n
14              fmt.Println(len(s2), cap(s2), &s2[0])
(gdb)
3 4 0xc820010248
15      }
(gdb) p &s2
$3 = (struct []int *) 0xc82003fe28
(gdb) x/24hb 0xc82003fe28
0xc82003fe28:   0x48    0x02    0x01    0x20    0xc8    0x00    0x00    0x00
0xc82003fe30:   0x03    0x00    0x00    0x00    0x00    0x00    0x00    0x00
0xc82003fe38:   0x04    0x00    0x00    0x00    0x00    0x00    0x00    0x00
```
s2的元素起始地址是0xc820010248，实际上是s1的第二个元素(0xc82003fe40)，长度是3和容量是4，比s1的对应值(长度4和容量5)相差1。
