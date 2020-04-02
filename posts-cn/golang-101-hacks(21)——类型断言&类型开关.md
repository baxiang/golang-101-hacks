注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译
通过类型断言(type assertion)方式来判断接口的具体类型，
Sometimes, you may want to know the exact type of an interface variable. In this scenario, you can use `type assertion`:

```
x.(T)

```
x”的类型必须为**interface**的变量，“T”表示是推断的类型。例如:
`x` is the variable whose type must be **interface**, and `T` is the type which you want to check. For example:

```
package main

import "fmt"

func printValue(v interface{}) {
    fmt.Printf("The value of v is: %v", v.(int))
}

func main() {
    v := 10
    printValue(v)
}

```

运行结果如下

```
The value of v is: 10

```
在上面的例子中，使用' v.(int) '来断言' v '是整数(int)类型。
In the above example, using `v.(int)` to assert the `v` is `int` variable.
如果“类型断言”推断错误，将会导致程序运行恐慌(panic):将下面断言语句
if the `type assertion` operation fails, a running panic will occur: change

```
fmt.Printf("The value of v is: %v", v.(int))  

```

修改成如下

```
fmt.Printf("The value of v is: %v", v.(string))

```
程序运行结果会出现错误


```
panic: interface conversion: interface is int, not string

goroutine 1 [running]:
panic(0x4f0840, 0xc0820042c0)
......

```
为了提高程序的健壮性，类型断言type assertion实际上返回一个额外的布尔变量来判断这个断言类型是否正确。将程序修改成如下:

To avoid this, `type assertion` actually returns an additional `boolean` variable to tell whether this operations holds or not. So modify the program as follows:

```
package main

import "fmt"

func printValue(v interface{}) {
    if v, ok := v.(string); ok {
        fmt.Printf("The value of v is: %v", v)
    } else {
        fmt.Println("Oops, it is not a string!")
    }

}

func main() {
    v := 10
    printValue(v)
}

```

这次运行结果如下

```
Oops, it is not a string!

```
此外，您还可以使用“type switch”，通过“type assertion”来确定变量的类型，并执行相应的操作。如下面的例子:
Furthermore, you can also use `type switch` which makes use of `type assertion` to determine the type of variable, and do the operations accordingly. Check the following example:

```
package main

import "fmt"

func printValue(v interface{}) {
    switch v := v.(type) {
    case string:
        fmt.Printf("%v is a string\n", v)
    case int:
        fmt.Printf("%v is an int\n", v)
    default:
        fmt.Printf("The type of v is unknown\n")
    }
}

func main() {
    v := 10
    printValue(v)
}

```

运行结果如下

```
10 is an int

```
与类型断言不同，“type switch”在括号中并没有指定的变量类型(例如“int”)，而使用了关键字`type`。
参考:
[Effective Go](https://golang.org/doc/effective_go.html);
[Go – x.(T) Type Assertions](https://codingair.wordpress.com/2014/07/21/go-x-t-type-assertions/);
[How to find a type of a object in Golang?](http://stackoverflow.com/questions/20170275/how-to-find-a-type-of-a-object-in-golang).
