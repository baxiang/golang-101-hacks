注：本文是对[golang-101-hacks](https://links.jianshu.com/go?to=https%3A%2F%2Fnanxiao.gitbooks.io%2Fgolang-101-hacks%2F)中文翻译,本文的[原文地址]([https://nanxiao.gitbooks.io/golang-101-hacks/posts/create-go-workspace.html](https://nanxiao.gitbooks.io/golang-101-hacks/posts/create-go-workspace.html)
)

当Go开发环境安装完成，接下来就是设置创建Go的工作目录了。
1 创建一个空文件夹作为工作区目录
```
# mkdir gowork
```
2 将创建的工作区目录设置成`$GOPATH`环境变量值
```
# cat /etc/profile
......
GOPATH=/root/gowork
export GOPATH
...... 
```
Go工作区包含3个子目录：
src:Go存放代码目录
pkg：存放包文件，你可以把它们看作是在链接阶段用来生成的依赖的库。
bin:存放可执行文件
看一个示例
1：在/root/gowork 我们设置的工作区下创建一个src文件夹

```
# mkdir src
# tree
.
└── src
1 directory, 0 files
```
go使用“包”概念组织源代码，并且每个“包”都会创建一个不同的目录，所以我在src中创建了一个greet目录：
```
# mkdir src/greet
Then create a new Go source code file (greet.go) in src/greet:
# cat src/greet/greet.go
package greet

import "fmt"

func Greet() {
        fmt.Println("Hello 中国!")
}
```
您可以理解这个greet目录提供了一个greet功能的包，它可以被其他程序使用。
（3）创建一个名字是hello包来调用greet包：
```
# mkdir src/hello
# cat src/hello/hello.go
package main

import "greet"

func main() {
        greet.Greet()
}
```
在hello.go文件中，main函数调用了greet包中的Greet函数 

(4) 现在整个工作区的目录结构是这样的：

```
# tree
.
└── src
    ├── greet
    │   └── greet.go
    └── hello
        └── hello.go

3 directories, 2 files
```
编译，安装hello 包
```
# go install hello
```
当前` $GOPATH` 目录结构
```
# tree
.
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
在bin文件夹中生成的可执行文件hello。因为hello需要依赖greet包，所以greet.a也会在pkg/linux_AMd64这个字目录中生成，它被存放在与当前系统编译环境相关联的。

运行hello
```
# ./bin/hello
Hello 中国!
```
(5)为了可以在任意目录下运行，可以将$GOPATH/bin的路径添加到$ATH的环境变量中

```
PATH=$PATH:$GOPATH/bin
export PATH
```
可以直接运行hello
```
# hello
Hello 中国!
```
