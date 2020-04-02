注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
在Go语言中，函数参数是值传递。使用slice作为函数参数时，函数获取到的是slice的副本:一个指针，指向底层数组的起始地址，同时带有slice的长度和容量。既然各位熟知数据存储的内存的地址，现在可以对切片数据进行修改。让我们看看下面的例子:
In Go, the function parameters are passed by value. With respect to use slice as a function argument, that means the function will get the copies of the slice: a pointer which points to the starting address of the underlying array, accompanied by the length and capacity of the slice. Oh boy! Since you know the address of the memory which is used to store the data, you can tweak the slice now. Let's see the following example:
```
package main

import (
    "fmt"
)

func modifyValue(s []int)  {
    s[1] = 3
    fmt.Printf("In modifyValue: s is %v\n", s)
}
func main() {
    s := []int{1, 2}
    fmt.Printf("In main, before modifyValue: s is %v\n", s)
    modifyValue(s)
    fmt.Printf("In main, after modifyValue: s is %v\n", s)
}
```
运行结果如下
```
In main, before modifyValue: s is [1 2]
In modifyValue: s is [1 3]
In main, after modifyValue: s is [1 3]
```
由此可见，执行modifyValue函数，切片s的元素发生了变化。尽管modifyValue函数只是操作slice的副本，但是任然改变了切片的数据元素，看另一个例子:
You can see, after running modifyValue function, the content of slice s is changed. Although the modifyValue function just gets a copy of the memory address of slice's underlying array, it is enough!
See another example:
```
package main

import (
    "fmt"
)

func addValue(s []int) {
    s = append(s, 3)
    fmt.Printf("In addValue: s is %v\n", s)
}

func main() {
    s := []int{1, 2}
    fmt.Printf("In main, before addValue: s is %v\n", s)
    addValue(s)
    fmt.Printf("In main, after addValue: s is %v\n", s)
}
```
The result is like this:
```
In main, before addValue: s is [1 2]
In addValue: s is [1 2 3]
In main, after addValue: s is [1 2]
```
而这一次，addValue函数并没有修改main函数中的切片s的元素。这是因为它只是操作切片s的副本，而不是切片s本身。所以如果真的想让函数改变切片的内容，可以传递切片的地址:
This time, the addValue function doesn't take effect on the s slice in main function. That's because it just manipulate the copy of the s, not the "real" s.
So if you really want the function to change the content of a slice, you can pass the address of the slice:
```
package main

import (
    "fmt"
)

func addValue(s *[]int) {
    *s = append(*s, 3)
    fmt.Printf("In addValue: s is %v\n", s)
}

func main() {
    s := []int{1, 2}
    fmt.Printf("In main, before addValue: s is %v\n", s)
    addValue(&s)
    fmt.Printf("In main, after addValue: s is %v\n", s)
}     
```
运行结果如下
```
In main, before addValue: s is [1 2]
In addValue: s is &[1 2 3]
In main, after addValue: s is [1 2 3]
```
