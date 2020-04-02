注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
Map是一种指向哈希表的引用类型，可以使用map构造一个“键值”类型的数据库，这在实际编程中非常高效。例如，下面的代码是统计切片中每个元素的总数:
```
package main

import (
    "fmt"
)

func main() {
    s := []int{1, 1, 2, 2, 3, 3, 3}
    m := make(map[int]int)

    for _, v := range s {
        m[v]++
    }

    for key, value := range m {
        fmt.Printf("%d occurs %d times\n", key, value)
    }
} 

```
运行结果
```
3 occurs 3 times
1 occurs 2 times
2 occurs 2 times

```

此外，根据[Go spec](https://golang.org/ref/spec#Map_types):“map是一个**无序的**元素组合，其中一种类型称为元素类型，另一种类型(称为键类型)是由惟一键索引组成。”如果再次运行上述程序，输出结果可能会有所不同:
Moreover, according to [Go spec](https://golang.org/ref/spec#Map_types): "A map is an **unordered** group of elements of one type, called the element type, indexed by a set of unique keys of another type, called the key type.". So if you run the above program another time, the output may be different:

```
2 occurs 2 times
3 occurs 3 times
1 occurs 2 times

```
你无法推测map的元素顺序。
map的键必须满足与“ ==”运算符进行比较：内置类型（如int，string等）满足此要求; 而切片不可以。对于struct类型，如果其成员都可以通过“ ==”运算符进行比较，那么此结构也可以用作键。
当您访问键不存在时，map将返回nil。即
```
package main

import (
    "fmt"
)

func main() {
    m := make(map[int]bool)

    m[0] = false
    m[1] = true

    fmt.Println(m[0], m[1], m[2])
}

```

输出结果如下

```
false true false

```
`m[0] `和` m[2] `的值都是' false '，所以您不能判断键是否真的存在在map中。解决方法是使用多返回值的“comma-ok”模式:
```
value, ok := map[key]

```
如果key存在 ok返回值是true 否则是false
有时，你可能不需要map的值，而只是将map作为一个集合使用。在这种情况下，您可以将map的值声明为空结构体:`struct{} `。如下面的例子:
```
package main

import (
    "fmt"
)

func check(m map[int]struct{}, k int) {
    if _, ok := m[k]; ok {
        fmt.Printf("%d is a valid key\n", k)
    }
}
func main() {
    m := make(map[int]struct{})
    m[0] = struct{}{}
    m[1] = struct{}{}

    for  i := 0; i <=2; i++ {
        check(m, i)
    }
}  

```
输出结果如下
```
0 is a valid key
1 is a valid key

```
使用内置函数`delete`删除map上的元素,即使这个键不存在也不会报错。
```
delete(map, key)

```
参考:
[Effective Go](https://golang.org/doc/effective_go.html);
[The Go Programming Language Specification](https://golang.org/ref/spec);
[The Go Programming Language](http://www.gopl.io/).
