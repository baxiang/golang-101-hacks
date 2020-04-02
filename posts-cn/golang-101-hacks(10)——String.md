注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
在Go中string是由不可变的字节数组构成的。一旦赋值，就不能修改字符串的值。例如
In `Go`, string is an immutable array of bytes. So if created, we can't change its value. E.g.:

```
package main

func main()  {
    s := "Hello"
    s[0] = 'h'
}

```
编译结果会提示错误：
The compiler will complain:

```
cannot assign to s[0] 

```
对字符串的修改可以将其转换为“byte”数组。但是实际上您并没有对原始字符串进行修改操作，改变的只是一个原始字符串的副本。

To modify the content of a string, you could convert it to a `byte` array. But in fact, you **do not** operate on the original string, just a copy:

```
package main

import "fmt"

func main()  {
    s := "Hello"
    b := []byte(s)
    b[0] = 'h'
    fmt.Printf("%s\n", b)
} 

```
运行结果
The result is like this:
```
hello

```
Go使用的是UTF-8的编码方式，函数len的结果是当前字符字节的长度而不是字符长度
Since `Go` uses `UTF-8` encoding, you must remember the `len` function will return the string's byte number, not character number:

```
package main

import "fmt"

func main()  {
    s := "日志log"
    fmt.Println(len(s))
} 

```
执行结果是
The result is:

```
9

```
由于每个中文占3个字节，上面的例子中s字符串有5个字符和9个字节组成的
Because each Chinese character occupied `3` bytes, `s` in the above example contains `5` characters and `9` bytes.
对字符串执行`for ... range`循环语句可以获取到每个字符
If you want to access every character, `for ... range` loop can give a help:

```
package main
import "fmt"

func main() {
    s := "日志log"
    for index, runeValue := range s {
        fmt.Printf("%#U starts at byte position %d\n", runeValue, index)
    }
}

```

执行结果
```
U+65E5 '日' starts at byte position 0
U+5FD7 '志' starts at byte position 3
U+006C 'l' starts at byte position 6
U+006F 'o' starts at byte position 7
U+0067 'g' starts at byte position 8

```
参考
[Strings, bytes, runes and characters in Go](https://blog.golang.org/strings);
[The Go Programming Language](http://www.gopl.io/).
