注：本文是对[golang-101-hacks](https://nanxiao.gitbooks.io/golang-101-hacks/)中文翻译。
[原文地址](https://nanxiao.gitbooks.io/golang-101-hacks/posts/go-get-command.html)

“go get”命令是下载和安装包以及相关依赖项的标准方法，让我们通过一个示例来说明go get使用细节：
（1）在GitHub创建项目[playstack](https://github.com/NanXiao/playstack) 
（2）项目的包含playstack文件夹下包含一个LICENSE文件和play目录
（3）play文件夹包含一个main.go文件
```
package main

import (
    "fmt"
    "github.com/NanXiao/stack"
)

func main() {
    s := stack.New()
    s.Push(0)
    s.Push(1)
    s.Pop()
    fmt.Println(s)
}

```
main包需要依赖:[stack]包(https://github.com/NanXiao/stack)。实际上，为了达到简单的演示目的，当前main() 函数的功能很简单，当前“playstack”的目录结构如下所示:
```
.
├── LICENSE
└── play
    └── main.go

1 directory, 2 files

```
清理`$GOPATH`目录，使用`go get`下载依赖包playstack

```
# tree
.

0 directories, 0 files
# go get github.com/NanXiao/playstack
package github.com/NanXiao/playstack: no buildable Go source files in /root/gocode/src/github.com/NanXiao/playstack

```
go get 会提示错误：没有可执行的go源文件 因为go get
“去获取”“命令抱怨”“没有可构建的去源文件…”，这是因为go get需要依赖包文件 而不是工程仓库目录，在playstack目录下是没有go源文件的，不是一个有效的包结构。
清空$GOPATH目录，然后执行`go get github.com/NanXiao/playstack/play`

```
# tree
.

0 directories, 0 files
# go get github.com/NanXiao/playstack/play
# tree
.
├── bin
│   └── play
├── pkg
│   └── linux_amd64
│       └── github.com
│           └── NanXiao
│               └── stack.a
└── src
    └── github.com
        └── NanXiao
            ├── playstack
            │   ├── LICENSE
            │   └── play
            │       └── main.go
            └── stack
                ├── LICENSE
                ├── README.md
                ├── stack.go
                └── stack_test.go

11 directories, 8 files
```
我们不仅可以看到“playstack”及其依赖项(“stack”)都已下载，而且可执行文件命令play和依赖库stack也会在存放到相对应的目录下。

“go get”命令背后的原理是获取包和依赖项的源码库(例如，使用“git clone”)，可以通过“go get -x”查看go get详细的执行流程:

```
# tree
.

0 directories, 0 files
# go get -x github.com/NanXiao/playstack/play
cd .
git clone https://github.com/NanXiao/playstack /root/gocode/src/github.com/NanXiao/playstack
cd /root/gocode/src/github.com/NanXiao/playstack
git submodule update --init --recursive
cd /root/gocode/src/github.com/NanXiao/playstack
git show-ref
cd /root/gocode/src/github.com/NanXiao/playstack
git submodule update --init --recursive
cd .
git clone https://github.com/NanXiao/stack /root/gocode/src/github.com/NanXiao/stack
cd /root/gocode/src/github.com/NanXiao/stack
git submodule update --init --recursive
cd /root/gocode/src/github.com/NanXiao/stack
git show-ref
cd /root/gocode/src/github.com/NanXiao/stack
git submodule update --init --recursive
WORK=/tmp/go-build054180753
mkdir -p $WORK/github.com/NanXiao/stack/_obj/
mkdir -p $WORK/github.com/NanXiao/
cd /root/gocode/src/github.com/NanXiao/stack
/usr/local/go/pkg/tool/linux_amd64/compile -o $WORK/github.com/NanXiao/stack.a -trimpath $WORK -p github.com/NanXiao/stack -complete -buildid de4d90fa03d8091e075c1be9952d691376f8f044 -D _/root/gocode/src/github.com/NanXiao/stack -I $WORK -pack ./stack.go
mkdir -p /root/gocode/pkg/linux_amd64/github.com/NanXiao/
mv $WORK/github.com/NanXiao/stack.a /root/gocode/pkg/linux_amd64/github.com/NanXiao/stack.a
mkdir -p $WORK/github.com/NanXiao/playstack/play/_obj/
mkdir -p $WORK/github.com/NanXiao/playstack/play/_obj/exe/
cd /root/gocode/src/github.com/NanXiao/playstack/play
/usr/local/go/pkg/tool/linux_amd64/compile -o $WORK/github.com/NanXiao/playstack/play.a -trimpath $WORK -p main -complete -buildid e9a3c02979f7c6e57ce4452278c52e3e0e6a48e8 -D _/root/gocode/src/github.com/NanXiao/playstack/play -I $WORK -I /root/gocode/pkg/linux_amd64 -pack ./main.go
cd .
/usr/local/go/pkg/tool/linux_amd64/link -o $WORK/github.com/NanXiao/playstack/play/_obj/exe/a.out -L $WORK -L /root/gocode/pkg/linux_amd64 -extld=gcc -buildmode=exe -buildid=e9a3c02979f7c6e57ce4452278c52e3e0e6a48e8 $WORK/github.com/NanXiao/playstack/play.a
mkdir -p /root/gocode/bin/
mv $WORK/github.com/NanXiao/playstack/play/_obj/exe/a.out /root/gocode/bin/play

```
从上面的输出中，我们可以看到首先git clone了“playstack”源码项仓库，然后是下载依赖项“stack”，最后执行编译和安装。
如果只是下载源文件，而不需要编译和安装，使用“' go get -d '”命令即可:
```
# tree
.

0 directories, 0 files
# go get -d github.com/NanXiao/playstack/play
# tree
.
└── src
    └── github.com
        └── NanXiao
            ├── playstack
            │   ├── LICENSE
            │   └── play
            │       └── main.go
            └── stack
                ├── LICENSE
                ├── README.md
                ├── stack.go
                └── stack_test.go

6 directories, 6 files

```
使用`go get -u`更新包文件和包之间的依赖


参考与引用:
[Command go](https://golang.org/cmd/go/#hdr-Download_and_install_packages_and_dependencies);
[How does "go get" command know which files should be downloaded?](https://groups.google.com/forum/#!topic/golang-nuts/-V9QR8ncf4w).
