注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
在Go语言中，长度值是数组类型的一部分。下面的代码声明了一个数组:
```
var array [3]int
```
而`var slice []int`声明的是一个切片类型。因此尽管是元素类型相同但长度不同的数组是不能相互赋值。例如:
```
package main

import "fmt"

func main() {
	var a1 [2]int
	var a2 [3]int
	a2 = a1
	fmt.Println(a2)
}
```
The compiler will complain:

cannot use a1 (type [2]int) as type [3]int in assignment
Changing "var a1 [2]int" to "var a1 [3]int" will make it work.
另一个需要注意的点是，下面的代码声明了一个数组，而不是一个切片:
```
array := [...]int {1, 2, 3} 
You can verify it by the following code:

package main

import (
	"fmt"
	"reflect"
)

func main() {
	array := [...]int {1, 2, 3}
	slice := []int{1, 2, 3}
	fmt.Println(reflect.TypeOf(array), reflect.TypeOf(slice))
}
```
输出结果如下
The output is:
```
[3]int []int
```
此外，在Go语言中，函数参数是值传递，如果使用数组作为函数参数，函数只是对数组副本进行操作。查看如下代码:
```
package main

import (
	"fmt"
)

func changeArray(array [3]int) {
	for i, _ := range array {
		array[i] = 1
	}
	fmt.Printf("In changeArray function, array address is %p, value is %v\n", &array, array)
}

func main() {
	var array [3]int

	fmt.Printf("Original array address is %p, value is %v\n", &array, array)
	changeArray(array)
	fmt.Printf("Changed array address is %p, value is %v\n", &array, array)
}   
```
输出结果是：
```
Original array address is 0xc082008680, value is [0 0 0]
In changeArray function, array address is 0xc082008700, value is [1 1 1]
Changed array address is 0xc082008680, value is [0 0 0]
```
通过打印日志中发现，changeArray函数中的数组地址与main函数中的数组地址是不一样的，导致原始数组的内容不会被修改。此外需要特别注意的是，如果数组非常大，在参数传递时，对数组进行复制开销会非常大。
