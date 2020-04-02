注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译

Go语言中的数据类型可分为两类:已命名和未命名。除了预先已声明的类型(如“int”、“rune”等)，还可以自己定义命名类型。例如:
```
type mySlice []int
```
未命名类型由文字类型定义。即， `[]int `是一个未命名的类型。
根据[Go spec](https://golang.org/ref/spec#Types)，每种类型都有一个底层类型:
Unnamed types are defined by type literal. I.e., `[]int` is an unnamed type.
According to [Go spec](https://golang.org/ref/spec#Types), there is an underlying type of every type:
每个类型T都有一个底层类型:如果T是预先声明的布尔型、数值型、字符串型或文字型中的一种，对应的底层类型就是T本身。否则，T的基础类型就是T在其类型声明中引用的类型的基础类型。

因此，在上面的示例中，命名类型“mySlice”和未命名类型“[]int”都具有相同的底层类型:“[]int”。
go有严格的变量赋值规则。例如:

```
package main

import "fmt"

type mySlice1 []int
type mySlice2 []int

func main() {
    var s1 mySlice1
    var s2 mySlice2 = s1

    fmt.Println(s1, s2)
}

```
编译会报错:
The compilation will complain the following error:
```
cannot use s1 (type mySlice1) as type mySlice2 in assignment
```
虽然' s1 '和' s2 '的底层类型相同:' []int '，但是它们属于' 两 '种不同的命名类型(' mySlice1 '和' mySlice2 ')，所以它们不能彼此赋值。但如果您将' s2 '的类型修改为' []int '，编译就没有问题:
Although the underlying type of `s1` and `s2` are same: `[]int`, but they belong to `2` different named types (`mySlice1` and `mySlice2`), so they can't assign values each other. But if you modify `s2`'s type to `[]int`, the compilation will be OK:

```
package main

import "fmt"

type mySlice1 []int

func main() {
    var s1 mySlice1
    var s2 []int = s1

    fmt.Println(s1, s2)
}

```
它背后的神奇之处在于一条规则[可转让性]([https://golang.org/ref/spec#Assignability](https://golang.org/ref/spec#Assignability)
)
x的类型V和T具有相同的底层类型，并且至少有一个V或T不是命名类型。
参考:
[Go spec](https://golang.org/ref/spec#Types);
[Learning Go - Types](http://www.laktek.com/2012/01/27/learning-go-types/);
[Golang pop quiz](https://twitter.com/davecheney/status/734646224696016901).
