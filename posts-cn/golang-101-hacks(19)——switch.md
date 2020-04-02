注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
和其他编程语言(例如C)相比，Go语音的`switch-case`语句不需要显式的添加“break”，也没有`fall-though`。如下面代码所示:
Compared to other programming languages (such as C), Go's switch-case statement doesn't need explicit "break", and not have fall-though characteristic. Take the following code as an example:
```
package main

import (
    "fmt"
)

func checkSwitch(val int) {
    switch val {
    case 0:
    case 1:
        fmt.Println("The value is: ", val)
    }
}
func main() {
    checkSwitch(0)
    checkSwitch(1)
}
```
输出结果是
```
The value is:  1
```
期望当val为0或1时，执行`fmt.Println("The value is: "， val)`，但实际上，该语句只在val为1时生效。为了得到期望结果，有两种方法:
使用fallthrough：
```
func checkSwitch(val int) {
    switch val {
    case 0:
        fallthrough
    case 1:
        fmt.Println("The value is: ", val)
    }
}
```
(2)将0和1放在同一个的case语句下:
```
func checkSwitch(val int) {
    switch val {
    case 0, 1:
        fmt.Println("The value is: ", val)
    }
}
```
与if-else相比，switch语句表达判断更清晰和简单：
```
package main

import (
    "fmt"
)

func checkSwitch(val int) {
    switch {
    case val < 0:
        fmt.Println("The value is less than zero.")
    case val == 0:
        fmt.Println("The value is qual to zero.")
    case val > 0:
        fmt.Println("The value is more than zero.")
    }
}
func main() {
    checkSwitch(-1)
    checkSwitch(0)
    checkSwitch(1)
}
```
输出结果
```
The value is less than zero.
The value is qual to zero.
The value is more than zero.
```
