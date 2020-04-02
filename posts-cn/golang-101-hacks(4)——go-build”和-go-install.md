查看当前` $GOPATH`目录结构，只展开src源码目录
Let's tidy up the` $GOPATH` directory and only keep Go source code files left over:
```
# tree
.
├── bin
├── pkg
└── src
    ├── greet
    │   └── greet.go
    └── hello
        └── hello.go

5 directories, 2 files
```
在源码greet包的greet.go文件中只有一个函数
```
# cat src/greet/greet.go
package greet

import "fmt"

func Greet() {
        fmt.Println("Hello 中国!")
}
```
虽然hello.go是一个main利用的包，greet可以构建到可执行的二进制文件中：
```
# cat src/hello/hello.go
package main

import "greet"

func main() {
        greet.Greet()
}
```
进入src/hello目录 执行go build命令
(1) Enter the src/hello directory, and execute go build command:
```
# pwd
/root/gowork/src/hello
# go build
# ls
hello  hello.go
# ./hello
Hello 中国!
```
我们可以看到在当前目录中创建了一个新的hello命令。
We can see a fresh hello command is created in the current directory.
查看` $GOPATH`目录
Check the `$GOPATH` directory:
```
# tree
.
├── bin
├── pkg
└── src
    ├── greet
    │   └── greet.go
    └── hello
        ├── hello
        └── hello.go

5 directories, 3 files
```
除了go build，还有另外一个终极命令
Compared before executing go build, there is only a final executable command more.
(2) Clear the $GOPATH directory again:
```
# tree
.
├── bin
├── pkg
└── src
    ├── greet
    │   └── greet.go
    └── hello
        └── hello.go

5 directories, 2 files
```
在hello目录下运行go install
Running go install in hello directory:
```
# pwd
/root/gowork/src/hello
# go install
#
```
Check the $GOPATH directory now:
```
# tree
.```
├── bin
│   └── hello
├── pkg
│   └── linux_amd64
│       └── greet.a
└── src
    ├── greet
    │   └── greet.go
    └── hello
        └── hello.go

6 directories, 4 files
```
不仅在bin目录中生成hello命令，而且在pkg/linux_AMd64中还生成了greet.a。而SRC文件夹没有任何变化，只包含源代码。

(3) There is -i flag in go build command which will install the packages that are dependencies of the target, but won't install the target. Let's check it:
```
# tree
.
├── bin
├── pkg
└── src
    ├── greet
    │   └── greet.go
    └── hello
        └── hello.go

5 directories, 2 files
```
Run go build -i under hello directory:
```
# pwd
#/root/gowork/src/hello
# go build -i
Check $GOPATH now:
# tree
.
├── bin
├── pkg
│   └── linux_amd64
│       └── greet.a
└── src
    ├── greet
    │   └── greet.go
    └── hello
        ├── hello
        └── hello.go
```
除了src/hello目录中的hello可执行文件外，也会在pkg/linux_amd64目录下生成的greet.a依赖库文件。
默认情况下，go build以当前目录的名作为编译后的二进制文件的名，要修改二进制文件名，可以使用-o标记

```
# pwd
/root/gowork/src/hello
# go build -o he
# ls
he  hello.go
```
可执行文件名称有hello变成了he
